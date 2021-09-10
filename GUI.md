## Table of content
* <a href="#description">Description</a>
* <a href="#how-it-works">How it works</a>
* <a href="#installation">Installation</a>
* <a href="#run-the-gui">Run the *GUI*</a>
* <a href="#adapatation">Adaptation</a>
* <a href="#screenshots">Screenshots</a>

## Description

The *GUI* builds an optional part of OBECA. Its main task is to show output from the *Receive Process*, *Gateway
Process* and *Application Process*.

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/concept-gui.png">


## How it works

The purpose of the *GUI* is to collect and display useful information from the *Receive Process*, *Gateway Process*
and *Application Process*. It also enables use cases where OBECA can be used for simple measurements (e.g., mobile
measurements) or as a standalone device (e.g., set top box, mobile phone/tablet showcase). The *GUI* consists of three
tabs, one for each process. Data from the *Receive Process* are collected by using the RestAPI of the *Receive Process*.

## Installation

The *GUI* was tested and verified on Ubuntu 20.04 LTS (64 bit).

````
sudo apt install python3-psutil vlc
sudo pip3 install python-vlc
git clone --recurse-submodules git@github.com:Austrian-Broadcasting-Services/obeca-gui.git

cd obeca-gui/
mkdir build && cd build

cmake -DCMAKE_INSTALL_PREFIX=/usr -GNinja ..
sudo ninja install
````

or alternatively, if you [imported the apt.bitstem.com repository](Receive-Process#Installing-Binaries):

````
sudo apt update
sudo apt install obeca-gui
````

## Run the *GUI*

````
cd /usr/share/obeca 
DISPLAY=:0 python3 obeca-gui.py 
````

> **_NOTE:_**  *GUI* is using the [*Receive Process* API](receive-process#restAPI) to collect information. If you are using a different port or https for the *Receive Process* please change the API calls in ``obeca-gui.py``

## Adaptation

The *GUI* mainly consists of python code ``obeca-gui.py`` and a css ``obeca-gui.css`` file to arrange and collect the
output.

## Screenshots

Below are some screenshots showing what the GUI looks like on a 7" touch display.
<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/gui-screen-empty.png">
<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/gui-screen-data.png">

