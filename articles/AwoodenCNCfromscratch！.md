---
title: "A wooden CNC from scratch！"
emoji: ""
type: ""
topics: []
published: false
---

* [1.Abstract](#1Abstract)
* [A. The goal of this project](#A-The-goal-of-this-project)
* [B. An overview of the CNC](#B-An-overview-of-the-CNC)
* [C. Implementation](#C-Implementation)  
   * [1) Basic mechanisms](#1-Basic-mechanisms)  
         * [1-1) Making main body and moving part](#1-1-Making-main-body-and-moving-part)  
         * [1-2) Making main board and motor driver boards](#1-2-Making-main-board-and-motor-driver-boards)  
   * [2) Interface with PC](#2-Interface-with-PC)  
   * [3) Improvements](#3-Improvements)  
         * [3-1) To machine more precisely and speedily](#3-1-To-machine-more-precisely-and-speedily)  
         * [3-2) To machine more safely](#3-2-To-machine-more-safely)
* [D. Machining](#D-Machining)  
   * [1） Wooden hexagonal cylinder (Fig. 24, Fig. 25)](#1Wooden-hexagonal-cylinder-Fig-24-Fig-25)  
   * [2）Carving a G clef (Fig. 26, Fig. 27)](#2Carving-a-G-clef-Fig-26-Fig-27)
* [E. References](#E-References)
* [F. Software](#F-Software)

### 1.Abstract

![f:id:pythonjacascript:20180813185406j:plain](/images/ppythonjacascript2018081320180813185406.jpg "f:id:pythonjacascript:20180813185406j:plain")  
(The CNC machine I made)

The challenge of this study is “to make a wooden CNC from scratch”. Normally, CNCs are made of metal owing to its high rigidity, however this time, I made it with wood, which is cheaper and easier to process than metal.

 The most serious issue of wood is its low rigidity, therefore, various kinds of problems happened, for example, vibration of the spindle that made machining very hard to work as expected.

 My solution is as follows: first, a coupling is attached between the spindle motor and the main shaft to reduce the vibration, then, the main shaft and a part of Z axis are made by using a lathe and a milling machine to keep the main shaft and end mill on a straight line with precision of ± 5μm.

 By applying ingenuity based on the causality analysis and utilizing the characteristics of wood, I solved many other problems by smoothly connecting the hard and soft-wares. For example, in order to avoid risks like end mill breaking, I made a software program to monitor the rotation of the end mill. In hardware, a limiter was attached to the end of each moving part (three axes).

 Even though the CNC I created is wooden, it achieved its performance to some extent by the skills coming from the feeling of sincerity and the search for precision to manufacturing. To think of this as a first step, I hope to combine such craftsmanship and state-of-the-art technology to create a new value.  
  
  
### A. The goal of this project

The goals of this project is to make a wooden CNC that goes around in likewise NC milling machine.  
  
### B. An overview of the CNC

CNC (Computer Numerical Control）is a milling machine which cuts and shapes designed material. An end mill, a kind of cutter, moves based on the data designed by CAD software working on PC. Normally, CNCs are made of a rigid substance like metal, however I challenged to create a wooden CNC in this study. Wood has unique features, one of which is that it is easier to process than metal, and is also moderate material that STUDENTS can get easily. However, various kinds of contraptions happened. I solved these problems by attaching new parts and utilizing the properties of wood. Further, some subsystems were attached to avoid the situation in which the end mill is broken in both software I coded and hard ware I made.   
  
  
### C. Implementation

#### 1) Basic mechanisms

![f:id:pythonjacascript:20180813190122p:plain](/images/ppythonjacascript2018081320180813190122.png "f:id:pythonjacascript:20180813190122p:plain")  
(Fig. 1 A typical structure of a CNC machine)

 Fig. 1 shows a typical structure of a CNC machine. Each axis has its own stepper motor which slides its moving parts. For example, the moving part goes up and down by the stepper motor in Z-axis (a red part in Fig. 1). The positions of the work and end mill are numerically controlled, and at the same time, the end mill rotates to machine the work.   
 These stepper motors work based on the signals from the main board. PC and the main board (a circuit board fixed on the CNC) are connected by a parallel cable. First, the main board receives the signals from PC though the parallel cable. Next, each stepper motor driver board (another circuit board fixed on it) reads these signals, and rotates each stepper motor (see Fig. 2).

![f:id:pythonjacascript:20180813190246p:plain](/images/ppythonjacascript2018081320180813190246.png "f:id:pythonjacascript:20180813190246p:plain")  
(Fig. 2 Basic diagram of electronics)  
  
##### 1-1) Making main body and moving part

 Almost all parts of this CNC are made of wood which is very cheap and easy to cut. They were made as follows: First, print out each plan of a part in actual size, second, attach it to a wood piece, which is then cut and drilled as designed by each plan. This CNC has wooden rails that support a moving part, (there are two rails and main shaft made of aluminium.)  
 The CNC has three main moving parts along X-, Y-, and Z- axes. Each moving part is supported by two rails and slides along the rails when a screw is turned by a stepper motor (Fig. 3). As a result, the CNC can make figures in three dimensions.  
![f:id:pythonjacascript:20180813190413p:plain](/images/ppythonjacascript2018081320180813190413.png "f:id:pythonjacascript:20180813190413p:plain")

  
![f:id:pythonjacascript:20180813190535p:plain](/images/ppythonjacascript2018081320180813190535.png "f:id:pythonjacascript:20180813190535p:plain")  
(Fig.4 Outward show of the CNC)

 Fig. 4 is a photograph of the CNC captured from the front. As you can see, the most parts are made of wood. A blue rectangle moves horizontally, a big red one moves vertically, and a green one moves in depth. The control board can be observed at the lower right with many switches. We can check several situations of CNC (ex., how limiter works etc.) by LEDs on the top surface of the board.  
  
##### 1-2) Making main board and motor driver boards

 All boards were designed by using “EAGLE” \[6\] and a parallel port I made is connected with the main board. Totally, four boards, the main board and three motor driver boards for X-, Y-, and Z-axes were made by myself.  
・Making motor driver boards  
 A main function of a motor driver board is to rotate a stepper motor by sending driving signals of A, A/, B, and B/ after converting the clock and direction signals from the main board. An IC called SLA7078MPRT, a kind of stepper motor driver, is installed. Circuit diagrams were drawn by referencing to \[8\] (an example of circuit diagram placed on the homepage of SLA7078MPRT) and \[9\] (see Figs. 5 and 6). Then, substrates were prepared based on circuit diagram. Fig. 7 and Fig. 8 shows a motor driver board.  
![f:id:pythonjacascript:20180813190705p:plain](/images/ppythonjacascript2018081320180813190705.png "f:id:pythonjacascript:20180813190705p:plain")

**\- Making main board (Fig. 9) and a parallel cable**  
The main board is connected to PC via a parallel cable.it is also made by myself (Fig.10). It has a role in interfacing between PC and the motor driver boards.   
![f:id:pythonjacascript:20180813190750p:plain](/images/ppythonjacascript2018081320180813190750.png "f:id:pythonjacascript:20180813190750p:plain")

**\- Making control board**  
 The control board is set at a remaining space of the CNC (The lower right of Fig. 4). It indicates the situation where the moving part of each axis is and the status of the power supply how much each part receives, and further a hardware switch for emergency is attached (see Fig. 11).  
![f:id:pythonjacascript:20180813190848p:plain](/images/ppythonjacascript2018081320180813190848.png "f:id:pythonjacascript:20180813190848p:plain")  
 (Fig. 11 A control board)  
  
#### 2) Interface with PC

 Software for sending G-code, CAM \[1\], and CAD \[2\] software downloaded from the Internet are used. It is difficult to send cutting data to CNC through cables expect for a parallel cable because MACH3 \[3\], a software to send cutting data to CNC, works only with a parallel cable. Therefore, by connecting the parallel port expansion board to the PCI slot of the PC and installing the driver (Fig. 12), it became possible to communication with the CNC (Fig. 13).  
![f:id:pythonjacascript:20180813190944p:plain](/images/ppythonjacascript2018081320180813190944.png "f:id:pythonjacascript:20180813190944p:plain")

#### 3) Improvements

##### 3-1) To machine more precisely and speedily

**\- Around the main shaft**   
By the vibration of the main shaft, it may take longer time to machine or might be impossible to machine according to the signal from the PC. Then, I improve as follows: First, a coupling is attached between the shaft of motor and main one, and made it absorb shaft shake（a coupling is drawn in yellow in Fig. 14）.   
![f:id:pythonjacascript:20180813191029j:plain](/images/ppythonjacascript2018081320180813191029.jpg "f:id:pythonjacascript:20180813191029j:plain")  
(Fig. 14 Main shaft)

  
Second, the main shaft and a part of Z-axis were made by using a lathe and a milling machine to keep the main shaft and end mill on a straight line with precision of ± 5μm.   

**\- To make the straightness and rigidity higher**  
A moving part of each axis moves along two rails in parallel (Fig. 3). In order to improve straightness and rigidity, the number of positions to stabilize each axis movement is increased, and several fine adjustments are applied. In X- and Z-axes, two more rails are attached as shown in Fig. 16.  
![f:id:pythonjacascript:20180813191155p:plain](/images/ppythonjacascript2018081320180813191155.png "f:id:pythonjacascript:20180813191155p:plain")  
(Fig. 16 Attached rails)

  
 Rails of X- and Z-axes are made of wood. However, the friction between woods is larger than friction between wood and aluminum, (this combination is used in Y-axis). In order to slide the moveable parts of these two axes smoothly, it is necessary to select of rail material and lubricant. In this study, oak is used as rails and graphite of pencils is used as a lubricant.   
  
  
##### 3-2) To machine more safely

・Emergency stop function by monitoring rotational speed of spindle  
 An emergency stop device is built that works when the rotational speed of the spindle motor would decreases. It can prevent the end mill from being damaged when it is stuck in the WORK. Once it worked, it would stop the operation of CNC. 

  
![f:id:pythonjacascript:20180813191319p:plain](/images/ppythonjacascript2018081320180813191319.png "f:id:pythonjacascript:20180813191319p:plain")  
(Fig. 17 Diagram of the emergency system)

Fig. 17 shows how it works. Timer0 function of a PIC is used to measure the rotation speed of main shaft, and if the measured value was smaller than a defined one, a signal sent to PC. Two PICs, 16F688 and 16F883 programmed with C and assembler language for this device are used. This device can also display the rotation on the 7-segment LEDs. These two PICs are transmitting and receiving the number of revolution with the communication program as follows: (1) measuring the number of revolutions, (2) sending this information through serial line, and (3) displaying the rotation number.

(1) Measuring the spindle speed   
First, an encoder that has two slits on the spindle is installed. Then, to count the pulses from the encoder, “Timer0” function of PIC is used. Pulses are counted by PIC in the following way in one second. When the TMR0 resister overflows, the T0IF bit becomes ”1” (Fig. 18). At that time, the variable OVERFLG is incremented. A data of rotation speed is stored in TMR0 resister and OVERFLG resister. Serial communication sends the value of the two resisters to PIC16F883\.   
![f:id:pythonjacascript:20180813191424p:plain](/images/ppythonjacascript2018081320180813191424.png "f:id:pythonjacascript:20180813191424p:plain")  
(Fig. 18 Frequency counting)

  
(2) Serial communication  
 Two PICs used able to send the data of rotation number PIC has a serial communication called EUSART capable to send 9 bits at once. However, the system must send 16 bit at once. Then, a serial communication program is coded by assembler. The program sends start bit first, then OVERFLW, and finally TMR0 data. It takes about 17ms. OVERFLW is a name of a variable name which is stored the number of overflow of TMR0 in one second. Fig. 19 shows the change in voltage a pin for communication.  
![f:id:pythonjacascript:20180813191521p:plain](/images/ppythonjacascript2018081320180813191521.png "f:id:pythonjacascript:20180813191521p:plain")  
(Fig. 19 Waveform of serial communication)

  
(3) Displaying the rotation number  
 In the receiver of the data, the received rotation number is converted to a decimal one and display it on the 5 segment LEDs that are dynamically controlled. If the received rotation number is so low that there is a risk of end mill’s breaking, it send a signal to PC and stop the CNC. It is possible to configuration the lowest rotation number which does not need to reset CNC, by using a volume. An A/D converter that has already installed in the PIC is used for this part. (Fig. 20)  
![f:id:pythonjacascript:20180813191611p:plain](/images/ppythonjacascript2018081320180813191611.png "f:id:pythonjacascript:20180813191611p:plain")  
(Fig. 20 A photograph of the system)

  
・Attaching limiters and a cover  
Each axis has two limiters at the end of movable region (three axes) (see Fig. 21). Shutter switches in disposable cameras were used for these limiters. Totally, six limiters are attached. Limiters can send a signal that shows each moving part comes to the end to PC and the control board. So, the situation of each limiters can be checked at the control panel or PC’s display.   
Also, to be safe out of CNC even if it happens the end mill is broken by any chance during machining, the CNC is covered with an acrylic plate.  
![f:id:pythonjacascript:20180813191703p:plain](/images/ppythonjacascript2018081320180813191703.png "f:id:pythonjacascript:20180813191703p:plain")  
(Fig. 21 An attached limiter)  
  
  
### D. Machining

I made some solids like below by using the CNC. 

#### 1） Wooden hexagonal cylinder (Fig. 24, Fig. 25)

![f:id:pythonjacascript:20180813191744p:plain](/images/ppythonjacascript2018081320180813191744.png "f:id:pythonjacascript:20180813191744p:plain")  

> **The data of the CNC**  
> Spindle speed=6,400rpm  
> Feed speed of X- and Y- axis=50mm/min  
> Feed speed of Z-axis=30mm/min  
> Final depth of machining = -6mm or -9mm  
> The diameter of end mill = 3mm

#### 2）Carving a G clef (Fig. 26, Fig. 27)

This time, I made a plate carved a G clef. For the purpose, I lay it in layers and set the depth to cut per layer.   
![f:id:pythonjacascript:20180813191833p:plain](/images/ppythonjacascript2018081320180813191833.png "f:id:pythonjacascript:20180813191833p:plain")  

> **The data of the CNC**  
> Spindle speed=6,400rpm   
> Feed speed of X- and Y-axis=50mm/min   
> Feed speed of Z-axis=30mm/min   
> Final depth of machining = see Table. 1  
> The diameter of end mill = 2mm

### E. References

\[1\] NC Viewer and Converter, National Institute of Technology, Maizuru College,  
<http://s-gikan2.maizuru-ct.ac.jp/xcl/>  
\[2\] a free CAD software <http://www.jwcad.net/>  
\[3\] a software, MACH3 <http://www.machsupport.com/software/downloads-updates/>  
\[4\] MPLAB, a software to program PIC<http://www.microchip.com/>  
\[5\] PIC Programmer Beta, another software to program PIC  
<http://akizukidenshi.com/catalog/contents2/ver4support.aspx>  
\[6\] a software to make schematic diagrams  
<http://www.autodesk.com/products/eagle/overview>  
\[7\] a software to carve letters <http://yusa.c.ooco.jp/mini%5Fcnc/moji.html>  
\[8\] a webpage referred to make substrates <http://jisaku-koubou.com/archives/2715>  
\[9\] a website referred to know how to use the stepper motor driver IC, (SLA7078MPRT)   
<http://www.semicon.sanken-ele.co.jp/sk%5Fcontent/sla7078mprt%5Fds%5Fjp.pdf>

### F. Software

NCVC …to make G-codes \[1\]  
JWCAD …to designing \[2\]  
MACH3 …to send signals to CNC \[3\]  
MPLAB …to make HEX files from C source file \[4\]  
PIC Programmer Beta …to write a program to a PIC \[5\]  
Eagle …to make schematic diagrams \[6\]  
Pseudo-linier-character…to change letters into data of diagrams \[7\]