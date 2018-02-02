Running CARLA without Display and Selecting GPUs 
------


This tutorial is designed for: 
- Remote server users that have several nvidia  graphical cards and want to effectively use CARLA on all GPUs.
- Desktop users who want to use the GPU that is not plugged on the screen for rendering CARLA.


On this tutorial you will learn.
- How to configure your server to have nvidia working on rendering without a display attached.
-  How to use VNC + VGL to simulate a display connected to any GPU you have in your machine.
- And Finally, how to run CARLA in this environment

This tutorial was tested in Ubuntu 16.04 and using NVIDIA 384.11 drivers.
## Preliminaries 

A few things need to be working in your server before.
 Latest NVIDIA Drivers, OpenGL,  VirtualGL(VGL), TurboVNC 2.11, ,
#### NVIDIA Drivers
Download and install NVIDIA-drivers with typical tutorials
http://www.nvidia.es/Download/index.aspx
#### OpenGL
Openg GL is necessary for Virtual GL. Normally OpenGL
can be installed through apt.

    sudo apt-get install freeglut3-dev mesa-utils
####  VGL
Follow this tutorial and install vgl: <br>
[Installing VGL](https://virtualgl.org/vgldoc/2_2_1/#hd004001)

#### TurboVNC
Follow the tutorial below to install TurboVNC 2.11:<br>
[Installing TurboVNC](https://cdn.rawgit.com/TurboVNC/turbovnc/2.1.1/doc/index.html#hd005001)

WARNING: Take care on which VNC you install as it may not be compatible with Unreal. The one above was the only one that worked for me. 
#### Extra Packages
These extra packages were necessary to make unreal to work.

    sudo apt install x11-xserver-utils libxrandr-dev




#### Configure your X

You must generate a X compatible with your nvdia and compatible to run without display. For that, the following command worked:

    sudo nvidia-xconfig -a --use-display-device=None --virtual=1280x1024



## Emulating The Virtual Display

Start by killing all Xorg servers. This command could change depending on your system.

    sudo service lightdm stop

Run your own Xorg. Here I use number 7, but it could be labeled with any free number.

    sudo nohup Xorg :7 &

Run an auxiliary remote VNC-Xserver. This will create a
virtual display  "8".

    /opt/TurboVNC/bin/vncserver :8

If everything is working fine the following command
should run smoothly.

    DISPLAY=:8 vglrun -d :7.0 glxinfo
Note. This will run glxinfo on Xserver 7, device 0. This means you are selecting the GPU 0 on your machine. To run on other GPU, such as GPU 1 run:

    DISPLAY=:8 vglrun -d :7.1 glxinfo

#### Extra

If you want disable the need of sudo when creating the  'nohup Xorg'
go to the '/etc/X11/Xwrapper.config'  file and change 'allowed_users=console' to 'allowed_users=anybody'


## Running CARLA 

Now, finally, to run CARLA on a certain gpu_number placed in a certain $CARLA_PATH, run.

    DISPLAY=:8 vglrun -d :7.<gpu_number> $CARLA_PATH/CarlaUE4/Binaries/Linux/CarlaUE4 