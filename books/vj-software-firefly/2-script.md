---
title: "【自作VJソフト・FireFly】スクリプト編"
emoji: ""
type: ""
topics: undefined
published: false
---

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/05/04/010134)  
のソフトウェアFireFlyのプログラムです。

本当はGitHubとかに挙げるべきなのでしょうが...。  
  
* [1.ファイル構成](#1ファイル構成)
* [mainApp.py](#mainApppy)
* [mainWindow.py](#mainWindowpy)
* [subWindow.py](#subWindowpy)
* [movie\_load.py](#movie%5Floadpy)

### 1.ファイル構成

ROOT  
 ├   
 │ ├今までに取り込んだ音声データ達（.wav）  
 │ └ 今までに取り込んだ動画データ達（.mp4）  
 ├   
 │ ├ bg.jpg  
 │ ├ black.jpg  
 │ ├ logo.jpg  
 │ ├ meter.png  
 │ ├ no\_img.jpg  
 │ ├ off.png  
 │ ├ on.png  
 │ └ play.gif  
 ├ mainApp.py  
 ├ mainWindow.py  
 ├ movie\_load.py  
 ├ read\_me.txt  
 └ subWindow.py 

  
こんな感じです。dataフォルダには再生する動画・音声データを保存して、次回起動するときにも同じ設定で使えるようにしています。  
figuresフォルダにはウィンドウの描画に必要な画像（スライダーの画像やウィンドウの背景画像など）を入れています。画像サイズを変えなければ各自で変更可能です。

実行ファイルは「mainApp.py」です。mainApp.pyからクラス継承で残りのpyファイルにアクセスしていきます。

因みに、使用しているライブラリは以下の通りです。

* pygame
* opencv ( cv2)
* tkinter
* ctypes
* numpy
* glob
* os
* shutil
* sys

  
では、本題のプログラムがこちら。

### mainApp.py

# -*- coding: utf-8 -*-
import cv2  #pip install opencv-python
import sys    
import serial  #pip install pyserial
from pygame import mixer
import numpy as np

from subWindow import subWin
from mainWindow import mainWin
from movie_load import *

class VJmain(mainWin, subWin):
    logo_image = cv2.imread("figures/logo.jpg", 1)
    Movie_Thumbnail = np.array((12,18))
    black_img = cv2.imread("figures/black.jpg")
    bg_image = cv2.imread("figures/bg.jpg", 1)
    no_img = cv2.imread("figures/no_img.jpg", 1)
    meter_image = cv2.imread("figures/meter.png",1)
    MovieList_length = 26
    group_num = 10
    keyBoard_lists = [["q", "w", "e", "r","t","y"],["a","s","d","f","g","h"],["z","x","c","v","b","n"]]
    font = cv2.FONT_HERSHEY_SIMPLEX
    
    def __init__(self, dual):
        self.wait_time = 24 
        self.volume = 100
        self.speed = 100
        
        self.dual = dual
        self.sounds = []
        self.MovieCaps = []
        self.sound_enable = []
        for i in range(12):
            self.sounds.append({})
            self.MovieCaps.append({})
            self.sound_enable.append({})

        self.current_monitor_img1 = self.bg_image  #再生する動画が変われば再描画（現在の画面そのまま）
        self.current_monitor_img2 = self.bg_image  #グループが変われば再描画（赤枠が描画されていない状態）
        self.Frame_numbers = []
        for i in range(10):
            self.Frame_numbers.append({})
        self.current_frame_number = 0
        self.current_all_frame_num = 0
        
        self.meter_move = 0

        mixer.init(frequency = 44100)
        self.LoadFiles()
        self.subWindowSetup()

        self.current_frame = []
        self.black_movie = cv2.VideoCapture("data/black.mp4")
        self.show = False
        self.group_playing = 1 #Config by Function keys
        self.group_reserve = 1
        self.playing_scene = "d"
        self.Thumbnails = self.getFirstFrames()
        self.setupWindowDestroy()
        self.resetSubWindow()
        if self.dual: self.MainWindowSetup()
    
        mixer.music.set_volume(1.0)
        cv2.setMouseCallback("sub_win", self.mouse_event)
        
    #############################################################
    #                MAIN LOOP
    ###################a##########################################
    def run(self):
        while True:
            ret, self.current_frame = self.MovieCaps[self.group_playing][self.playing_scene].read()
            if not ret:
                self.current_frame = self.black_img
            else: self.current_frame_number += 1
            if self.dual: self.update_mainWin()
            self.update_subWin()
             
            # qキーが押されたら途中終了
            key2 = cv2.waitKey(self.wait_time) & 0xff
            if(key2 is not 255):
                if(47 < key2 and key2 < 58):
                        self.group_reserve = key2-48
                        print("group changed to ->" + str(key2-48))
                        self.resetSubWindow()
                
                elif(chr(key2) in self.MovieCaps[self.group_reserve].keys()):
                        print("scene changed to ->" + chr(key2))
                        self.current_frame_number = 0
                        self.ChangeScene_mainWin(chr(key2))
                        self.ChangeScene_subWin()
                                                
                elif key2 == 27: #ESC
                    break
                elif key2 == 43: #Speed Up
                    self.speed = max(self.speed-1, 10)
                    self.resetSubWindow("movie play speed-->"+str(self.speed)+"%")
                    self.wait_time = int(2400/self.speed)
                elif key2 == 45:
                    self.speed = min(self.speed+1, 200)
                    self.resetSubWindow("movie play speed-->"+str(self.speed)+"%")
                    self.wait_time = int(2400/self.speed)
                    print("speed up")

        
    def OpenPort(self, port_name="COM5"):
        ser=serial.Serial(port_name, 19200, timeout=1) 
        print("Connected!!")
        return ser
    
    def exitApp(self):
        self.exitMainWin()
        self.ExitSubWin()
        sys.exit()

        
    def mouse_event(self, event, x, y, flags, param):
        if(event == cv2.EVENT_RBUTTONUP):
            if(x<440 and y>390):
                print("movie choosed -> start configuration")
                width = int((x-35)/67)
                height = int((y-390)/60)
                key_char = self.keyBoard_lists[height][width]
                Movie, Audio, audio_enable = edit_one_scene(self.group_reserve, key_char)
                print(key_char)
                print(Movie, Audio, audio_enable)
                if(Movie is not None):
                    self.MovieCaps[self.group_reserve][key_char] = Movie
                    self.MovieCaps[self.group_reserve][key_char].set(cv2.CAP_PROP_POS_FRAMES, 0)
                    self.Frame_numbers[self.group_reserve][key_char] = self.MovieCaps[self.group_reserve][key_char].get(cv2.CAP_PROP_FRAME_COUNT)
                    print("finished loading video")
                    if(Audio is not None):
                        self.sounds[self.group_reserve][key_char] = Audio
                        self.sound_enable[self.group_reserve][key_char] = audio_enable
                    self.Thumbnails[self.group_reserve][key_char] = self.getFirstFrame(self.group_reserve, key_char)
                    self.resetSubWindow()
                    
        elif(event == cv2.EVENT_LBUTTONDOWN and 373<y and 562>y):
            if(480<x and x<510):self.meter_move = 1
            elif(550<x and x<580): self.meter_move = 2
            elif(840<x and x<860): self.meter_move = 4
            print("meter move" + str(self.meter_move))
        elif(self.meter_move>0 and event == cv2.EVENT_MOUSEMOVE):
            if self.meter_move == 2:
                self.speed = max(int((562-y)/0.945), 10)
                self.speed = min(200, self.speed)
                print("movie play speed-->"+str(self.speed)+"%")
                self.resetSubWindow("movie play speed-->"+str(self.speed)+"%")
                self.wait_time = int(2400/self.speed)
                
            elif self.meter_move == 1:
                self.volume = max(int(307-y/1.8), 0)
                self.volume = min(100, self.volume)
                print("Audio Volume-->"+str(self.volume)+"%")
                self.resetSubWindow("Audio Volume-->"+str(self.volume)+"%")
                if self.sound_enable[self.group_playing][self.playing_scene]:
                    self.sounds[self.group_playing][self.playing_scene].set_volume(self.volume/100.)
                  
        if(event == cv2.EVENT_LBUTTONUP):
            self.meter_move=0

import ctypes  # An included library with Python install.   
def check_dual():
    a = ctypes.windll.user32.MessageBoxW(0, "フルスクリーン、デュアルモニタで使用しますか？", "CAUTION", 4)
    return a==6

print("system start")
MainApp = VJmain(check_dual())
mixer.init(frequency = 44100)
MainApp.run()
MainApp.exitApp()

### mainWindow.py

# -*- coding: utf-8 -*-
import cv2  #pip install opencv-python
import sys    
import serial  #pip install pyserial
from pygame import mixer  #pip insstall pygame
import numpy as np
import os

class mainWin():
    def __init__(self):
        pass
        #sys.exit()
        
    #############################################################
    #                LOAD FILES
    #############################################################
    
    def LoadFiles(self):
        #Audio
        for i in range(10):
            for name in ["q", "w", "e", "r","t","y","a","s","d","f","g","h","z","x","c","v","b","n"]:
                if(os.path.exists("data/"+str(i)+name+".wav")):
                    self.sounds[i][name] = mixer.Sound("data/"+str(i)+name+".wav")
                    print("audio loaded --> "+str(i)+name+".wav")
                    self.sound_enable[i][name] = True
                else:
                    self.sound_enable[i][name] = False
    
                #Video
                if(os.path.exists("data/"+str(i)+name+".mp4")):
                    self.MovieCaps[i][name] = cv2.VideoCapture("data/"+str(i)+name+".mp4")
                    self.MovieCaps[i][name].set(cv2.CAP_PROP_FPS, 30)
                    print("video loaded --> "+str(i)+name+".mp4")
                    self.Frame_numbers[i][name] = self.MovieCaps[i][name].get(cv2.CAP_PROP_FRAME_COUNT)
                    
    def MainWindowSetup(self):
        cv2.namedWindow("screen", cv2.WINDOW_NORMAL)
        cv2.moveWindow("screen", 1925, 0)
        cv2.setWindowProperty("screen", cv2.WND_PROP_FULLSCREEN, cv2.WINDOW_FULLSCREEN)


    def getFirstFrames(self):
        #各動画の一枚目の画像を取り込む
        frames= []
        for i in range(self.group_num):
            frames.append({})
            
        for g in range(self.group_num):
            for i in range(26):
                if(chr(i+97) in self.MovieCaps[g].keys()):
                    frames[g][chr(i+97)] = self.getFirstFrame(g, chr(i+97))
        return frames
        
    def getFirstFrame(self, group, scene):
        self.MovieCaps[group][scene].set(cv2.CAP_PROP_POS_FRAMES, 20) 
        _, frame = self.MovieCaps[group][scene].read()
        self.MovieCaps[group][scene].set(cv2.CAP_PROP_POS_FRAMES, 0) 
        return cv2.resize(frame, (63,40))
            
            
    #############################################################
    #                MAIN LOOP
    #############################################################
    def ChangeScene_mainWin(self, scene_str):
        if(self.sound_enable[self.group_playing][self.playing_scene]):
            self.sounds[self.group_playing][self.playing_scene].stop()
        self.MovieCaps[self.group_playing][self.playing_scene].set(cv2.CAP_PROP_POS_FRAMES, 0) 
        
        self.playing_scene = scene_str
        self.group_playing = self.group_reserve
        if(self.sound_enable[self.group_playing][self.playing_scene]):
            print("audio start")
            #print(self.sounds[self.group_playing][self.playing_scene])
            self.sounds[self.group_playing][self.playing_scene].set_volume(self.volume/100.)
            self.sounds[self.group_playing][self.playing_scene].play()    
    
            
    def update_mainWin(self):
        cv2.imshow("screen", self.current_frame)
        
    def exitMainWin(self):
        if self.sound_enable[self.group_playing][self.playing_scene]:
            self.sounds[self.group_playing][self.playing_scene].stop()
        mixer.music.stop()
        
        for i in range(self.group_num):
            for name in self.MovieCaps[i].keys():
                self.MovieCaps[i][name].release()
        cv2.destroyWindow("screen")
  
  
### subWindow.py

# -*- coding: utf-8 -*-
import cv2
import numpy as np
class subWin():
    def __init__(self):
        pass
        
    def subWindowSetup(self):
        cv2.namedWindow("setup", cv2.WINDOW_AUTOSIZE)
        #cv2.resizeWindow("setup", 540, 300)
        #cv2.moveWindow("setup", 30, 0)
        cv2.imshow("setup", self.logo_image)
        cv2.waitKey(1)
        
    def setupWindowDestroy(self):
        cv2.destroyWindow("setup")
        cv2.namedWindow("sub_win", cv2.WINDOW_NORMAL)
        cv2.resizeWindow("sub_win", 1072, 602)
        cv2.moveWindow("sub_win", 30, 0)
        
        
    def resetSubWindow(self, label_text=""):
        self.current_monitor_img2 = np.array(self.bg_image)
        for name in self.Thumbnails[self.group_reserve].keys():
            key_pos =np.argwhere(np.array(self.keyBoard_lists)==name)
           
            self.current_monitor_img2[410+key_pos[0][0]*60:450+key_pos[0][0]*60, 35+key_pos[0][1]*67:98+key_pos[0][1]*67] = \
                                    self.Thumbnails[self.group_reserve][name]
        cv2.putText(self.current_monitor_img2,label_text,(650,280), self.font, 0.5,(255,255,255),0,cv2.LINE_AA)
        self.current_monitor_img2[543-int(self.speed*0.9):580-int(self.speed*0.9),552:577] = self.meter_image
        self.current_monitor_img2[523-int(self.volume*1.8):560-int(self.volume*1.8),480:505] = self.meter_image
        self.ChangeScene_subWin()
        
        
    def update_subWin(self):
        ####################################################
        #       MOVIE FRAMES
        ####################################################
        img = np.array(self.current_monitor_img1)
        percentage = int(self.current_frame_number * 103 / self.current_all_frame_num)
        cv2.rectangle(img,(590, 404), (592+percentage, 412),(0,0,255),-1) 
        cv2.putText(img,str(self.current_frame_number)+"/"+ str(self.current_all_frame_num),(535,430), self.font, 0.5,(255,255,255),0,cv2.LINE_AA)
        
        #img[6:341,23:491] = self.ResizedMovieCaps[self.group_playing][self.playing_scene][self.current_frame_number]
        img[6:341,23:491] = cv2.resize(self.current_frame, (468,335))
        cv2.imshow("sub_win", img)


    def ChangeScene_subWin(self):
        key_pos =np.argwhere(np.array(self.keyBoard_lists)==self.playing_scene)
        self.current_monitor_img1 = np.array(self.current_monitor_img2)
        cv2.rectangle(self.current_monitor_img1,(35+key_pos[0][1]*67, 410+key_pos[0][0]*60), (98+key_pos[0][1]*67, 450+key_pos[0][0]*60),(255,255,0),2)        
        cv2.rectangle(self.current_monitor_img1,(123+self.group_reserve*30, 354), (147+self.group_reserve*30, 376),(0,255,255),2)
        cv2.putText(self.current_monitor_img2,"Playing->"+str(self.group_playing)+self.playing_scene,(600,250), self.font, 0.8,(255,255,255),2,cv2.LINE_AA)
        self.current_all_frame_num = self.Frame_numbers[self.group_playing][self.playing_scene]


    def ExitSubWin(self):
        print("sub window destroy")
        cv2.destroyWindow("sub_win")
  
  
### movie\_load.py

import os
import tkinter
import tkinter.filedialog
import tkinter.messagebox
import tkinter.ttk as ttk
from tkinter import font
import cv2  #pip install opencv-python
import sys
from pygame import mixer
import glob
import shutil

#############################################################################
#                         Edit One Scene
#############################################################################
class EditOneScene(tkinter.Frame):
    def __init__(self, master=None, group=1, scene="a"):
        super().__init__(master)
        self.pack()
        self.MovieFileName = tkinter.StringVar()
        self.AudioFileName = tkinter.StringVar()
        self.audio_pre_playing = False
        self.group = group
        self.scene = scene
        
        #self.MovieBtn = tkinter.Button(self, bg='#808080', fg='#ffffff', text="Change MOVIE", command=self.LoadNewMovie, width=80, height = 40)
        MovieBtn = tkinter.Button(bg='#808080', fg='#ffffff', text="Change MOVIE", command=self.LoadNewMovie)
        MovieBtn.place(x=250, y=20, height=40, width=90)

        AudioBtn = tkinter.Button(bg='#808080', fg='#ffffff', text="Change Audio", command=self.LoadNewAudio)
        AudioBtn.place(x=250, y=70, height=40, width=90)
        
        VideoPlayBtn = tkinter.Button(bg='#808080', fg='#ffffff', text="Play", command=self.PlayMovie)
        VideoPlayBtn.place(x=350, y=20, width=50, height = 40)
        
        AudioPlayBtn = tkinter.Button(bg='#808080', fg='#ffffff', text="Play", command=self.PlayAudio)
        AudioPlayBtn.place(x=350, y=70, width=50, height = 40)
        
        self.val = tkinter.BooleanVar()
        self.val.set(False)
        chkBtn = tkinter.Checkbutton(text='', variable = self.val)
        chkBtn.place(x=30, y=130)

        self.ButtonQuit = tkinter.Button(bg='#808080', fg='#ffffff', text="更新", command=self.SaveData)
        self.ButtonQuit.place(x=210, y=120, width=150, height = 40)    
        
        MoviefNameEntry = ttk.Entry(textvariable= self.MovieFileName)
        MoviefNameEntry.place(x=10, y=20, width=230, height = 40)
        
        AudiofNameEntry = ttk.Entry(textvariable= self.AudioFileName)
        AudiofNameEntry.place(x=10, y=70, width=230, height = 40)

        font3 = font.Font(family='Times', size=15)
        font2 = font.Font(family='Times', size=10)
        label = tkinter.Label(text="Enable Audio",bg = "black",fg="white", font=font3)
        label.place(x=50, y=130)
        self.log = tkinter.StringVar()
        self.log.set("log here")
        label2 = tkinter.Label(textvariable=self.log, bg = "#c0c0c0",fg="black", font=font2, justify='left')
        label2.place(x=15, y=175, width=400, height = 100)
        
        self.MovieFileName.set("Movie path here")
        self.AudioFileName.set("Audio path here")

        self.Movie = None
        self.audio = None
        
    def getPath(self, fType= [("","*")], title = "Open", msg = "choose one file"):
        iDir = os.path.abspath(os.path.dirname(__file__))
        file = tkinter.filedialog.askopenfilename(filetypes = fType,initialdir = iDir)
        return file
        
    def LoadNewMovie(self):
        self.log.set("Load New Video")
        file_path = self.getPath(fType=[("","*.mp4")], title = "Open", msg = "choose one MOVIE file")
        if file_path == "": return 0
        else:
            self.Movie = cv2.VideoCapture(file_path)
            self.Movie.set(cv2.CAP_PROP_FPS, 30)
        self.MovieFileName.set(file_path)
        self.ButtonQuit["bg"] = "yellow"
        self.log.set("Loaded New Video\n--> "+ file_path+"\nplease update the data!!")


    def LoadNewAudio(self):
        self.log.set("Load New Audio")
        file_path = self.getPath(fType=[("","*.wav")], title = "Open", msg = "choose one MOVIE file")
        if file_path == "": return 0
        else:
            self.audio=mixer.Sound(file_path)
            self.val.set(True)
        self.AudioFileName.set(file_path)
        self.ButtonQuit["bg"] = "yellow"
        self.ButtonQuit["fg"] = "black"
        self.log.set("Loaded New Audio\n--> "+ file_path+"\nplease update the data!!")

            
    def QuitApp(self):
        print("quit this App")
        self.master.destroy()
        
    def PlayMovie(self):
        f_name = self.MovieFileName.get()
        self.log.set("Start video playing\n--> "+ f_name)
        cap =cv2.VideoCapture(self.MovieFileName.get())
        while True:
            ret, frame = cap.read()
            if not ret: 
                break
            cv2.imshow('frame',frame)
            if cv2.waitKey(23)&0xff==27: break
        cap.release()
        cv2.destroyWindow('frame')
        self.log.set("Finished video playing\n--> "+ f_name)
    
    def PlayAudio(self):
        f_name = self.AudioFileName.get()
        if self.audio_pre_playing:
            self.log.set("Stop audio\n--> "+ f_name)
            self.audio.stop()
            self.audio_pre_playing =False
        else:
            self.log.set("Audio playing\n--> "+ f_name)
            if(os.path.exists(f_name)):
                self.audio.play()
                self.audio_pre_playing = True

    def SaveData(self):
        if(os.path.exists(self.MovieFileName.get())):
            self.log.set("Saving Video Data\n-->"+str(self.group)+self.scene+".mp4")
            shutil.copyfile(self.MovieFileName.get(),"data/"+str(self.group)+self.scene+".mp4")
        if(os.path.exists(self.AudioFileName.get())):
            self.log.set("Saving Audio Data-->"+str(self.group)+self.scene+".wav")
            shutil.copyfile(self.AudioFileName.get(), "data/"+str(self.group)+self.scene+".wav")
        self.log.set("Finished Video Data")
        self.ButtonQuit["bg"] = "black"

        
def edit_one_scene(group, scene):
    root = tkinter.Tk()
    root.geometry("430x280")
    root.configure(bg='#000000')
    app = EditOneScene(root, group, scene)
    app.mainloop()
    if app.audio_pre_playing:
        app.audio.stop()
    return app.Movie, app.audio, app.val.get()