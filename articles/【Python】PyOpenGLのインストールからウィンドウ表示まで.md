---
title: "【Python】PyOpenGLのインストールからウィンドウ表示まで"
emoji: ""
type: ""
topics: []
published: false
---

* [PyOpenGLについて](#PyOpenGLについて)
* [ライブラリのインストール](#ライブラリのインストール)
* [実行](#実行)
* [実行結果](#実行結果)

### PyOpenGLについて

PyOpenGLは、OpenGLのPython版です。OpenGLは**2次元/3次元のグラフィック**ライブラリのことです。  
  
### ライブラリのインストール

pip install PyOpenGL

pip install PyOpenGL_accelerate

この2つのコマンドを実行します。

一つ目のコマンドは問題なくインストールできたのですが、二つ目で以下のエラーが発生。

error: Microsoft Visual C++ 9.0 is required. Get it from http://aka.ms/vcpython27

ということで、以下のサイトにアクセスし、Downloadボタンを押してダウンロード。  
[Download Microsoft Visual C++ Compiler for Python 2.7 from Official Microsoft Download Center](http://aka.ms/vcpython27)

ダウンロードした「**VCForPython27.msi**」を実行して、Python2.7用のC++コンパイラを入れる。

  
それでもまだエラーが出る場合は、PyOpenGLとは別に、**GLUT**をインストールする必要があるかもしれないので  
以下のサイトの、  
[freeglut Windows Development Libraries](https://www.transmissionzero.co.uk/software/freeglut-devel/)

「**Download freeglut 3.0.0 for MinGW**」をクリックして、「**freeglut-MSVC-3.0.0-2.mp.zip**」をダウンロード＆解凍してください。  
そして、zipファイル内の「freeglut\\bin\\x64\\」にある「**freeglut.dll**」を「C:\\Windows\\System32」の中にコピー（移動）してください。  
  
  
### 実行

以下のプログラムを実行します。

こちらのページを参考にしました：  
[pyOpenGL を Windows8 (64bit) にインストールする | n3956.net](http://n3956.net/blog/?p=171)  

from OpenGL.GL import *
from OpenGL.GLU import *
from OpenGL.GLUT import *
import sys

def main():
    glutInit(sys.argv)
    glutInitDisplayMode(GLUT_RGB | GLUT_SINGLE | GLUT_DEPTH)
    glutInitWindowSize(300, 300)     # window size
    glutInitWindowPosition(100, 100) # window position
    glutCreateWindow(b"teapot")      # show window
    glutDisplayFunc(display)         # draw callback function
    glutReshapeFunc(reshape)         # resize callback function
    init(300, 300)
    glutMainLoop()

def init(width, height):
    """ initialize """
    glClearColor(0.0, 0.0, 0.0, 1.0)
    glEnable(GL_DEPTH_TEST) # enable shading

    glMatrixMode(GL_PROJECTION)
    glLoadIdentity()
    ##set perspective
    gluPerspective(45.0, float(width)/float(height), 0.1, 100.0)

def display():
    """ display """
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
    glMatrixMode(GL_MODELVIEW)
    glLoadIdentity()
    ##set camera
    gluLookAt(0.0, 1.0, 5.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0)
    ##draw a teapot
    glColor3f(1.0, 0.0, 0.0)
    glutWireTeapot(1.0)   # wireframe
#    glutSolidTeapot(1.0)  # solid
    glFlush()  # enforce OpenGL command

def reshape(width, height):
    """callback function resize window"""
    glViewport(0, 0, width, height)
    glMatrixMode(GL_PROJECTION)
    glLoadIdentity()
    gluPerspective(45.0, float(width)/float(height), 0.1, 100.0)

if __name__ == "__main__":
    main()
  
  
### 実行結果

![f:id:pythonjacascript:20181229095609j:plain](/images/ppythonjacascript2018122920181229095609.jpg "f:id:pythonjacascript:20181229095609j:plain")  
このような画面が表示されればOKです。