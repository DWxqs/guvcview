Guvcview for OPI with CMOS Camera (H3 - A64 - A83T) / NanoPi M2 / M3 with USB camera
*************************************************************************************

This is my modified version (2.0.2) that works with OPI (Orange Pi) PC / One / 2Plus / 2E
and BananaPi M64 with CMOS camera and if worka with NanoPi M2 / M3 USB camera (to be updated).

Watch Armbian for Updates and DEB packages: http://www.armbian.com/

I strongly suggest Armbian for you OPI/A64 for best performance.

	Important:
	If you have guvcview stock version installed already, please remove it completely 
	before install this.
	Usually you can do this: sudo apt-get purge guvcview
	Failing to remove the stock version and its libgview will break this hacked version

Known issues:
 * (FIXED) Changing resolution on the fly may lock gucview and you will need to kill the process
 * Encoding is done by software and not hardware. If you want to use Hardware encoder use FFMPEG provided in another repo.
 * Encoding video stream may end with only 2 fps and sound out of sync, better use a heatsink on your board
 * Start guvcview via command line with the correct parameters
 * Cannot change FPS

Known issues BananaPi M64 / Pine64+:
 * Controls not working yet
 * (FIXED) Some artifacts (need some investigation)

Known issues BananaPi M3 (ov8865 - 8M sensor - 3264 x 2448):
 * (FIXED) Image from ov8865 is kind of greenish (using stock ov8865 driver, no doc available to adjust it yet)
 * (FIXED) For some unkown reason, starting grabbing video with 1920x0180 works better
 * If guvcview fails for any reason, the memmory buffer fails to be mapped on next time you run guvcview and board needs a reboot (don't worry, board still stable, it is just for the camera)

Howto Build on OPI / Pine64+ 
 * clone the repo (OPI/A64): git clone https://github.com/avafinger/guvcview.git
 * cd guvcview
 * Install dependencies (see below)
 * Install linux-headers for your kernel if not already installed (usually /usr/src/linux-headers-your_kernel_version)
 some may find /usr/src/linux-headers-your_kernel_version-sunxi, if that happens you need to edit Makefile.am and add the sufix '-sunxi'.
 * Run (Debian Jessie):

Requirement for NanoPi M2/M3: (use this branch: git clone -b NanoPi_M2_M3 )
 * USB camera (MJPG)
 * NanoPi M2/M3: git clone -b NanoPi_M2_M3 https://github.com/avafinger/guvcview.git

Dependencies:
=============
	sudo apt-get install intltool libtool autotools-dev libsdl1.2-dev libgtk-3-dev portaudio19-dev libpng12-dev libavcodec-dev libavutil-dev libudev-dev libusb-1.0-0-dev libpulse-dev libgsl0-dev libv4l-dev libv4l2rds0 libsdl2-dev

./bootstrap.sh

./configure --prefix=/usr --enable-yuyv --disable-sdl2

make

sudo make install

 * Check the version:
guvcview --version 

Guvcview version 2.0.2

How to use Guvcview with USB Camera on NanoPi M2/M3
===================================================
* checkout with -b NanoPi_M2_M3 like so: git clone -b NanoPi_M2_M3
* Attach your USB camera (MJPG)
* Start Guvcview always with command line:

guvcview -d /dev/video0 -x 640x480 -r sdl -f MJPG

or any other valid window size:

guvcview -d /dev/video0 -x 1280x720 -r sdl -f MJPG


How to use Guvcview with GC2035 (all Orange PIs)
================================================

 * Make sure you have the camera driver loaded:
type in command line:

	modprobe gc2035 hres=0 mclk=34

	modprobe vfe_v4l2

 * Check if you have /dev/video0 (our camera)
 * Run guvcview by command line parameters like this:
type:

	guvcview -d /dev/video0 -x 640x480 -r sdl -f yu12

	or another resolution

	guvcview -d /dev/video0 -x 1280x720 -r sdl -f yu12


How to use Guvcview with s5k4ec (Pine64+):
=========================================

 * get latest kernel version 3.10.102 (longsleep) with s5k4ec DTB "ok"
 * Xenial 16.04 LTS
 * (FIXED) always run guvcview by command line:
	
	
	guvcview -d /dev/video0 -x 1280x720 -r sdl -f yu12

	guvcview -d /dev/video0 -x 1920x1080 -r sdl -f yu12

	or run just guvcview or from the menu

How to use Guvcview with ov5640 (BananaPi M64 / Pine64+):
=========================================================

 * get latest kernel version 3.10.102 (longsleep) with ov5640 DTB enabled
 * Xenial 16.04 LTS
 * (FIXED) always run guvcview by command line:


	guvcview -d /dev/video0 -x 1280x720 -r sdl -f yu12

	guvcview -d /dev/video0 -x 1920x1080 -r sdl -f yu12

	or run just guvcview or from the menu

How to use Guvcview with ov8865 (BananaPi M3):
==============================================

 * get linux-sunxi version 3.4.39
 * Xenial 16.04 LTS
 * (FIXED) always run guvcview by command line FIRST with 1920x1080:


	guvcview -d /dev/video0 -x 1920x1080 -r sdl -f yu12

	or run just guvcview or from the menu

Running guvcview with some USB cameras
--------------------------------------
 * If you want to run this version with USB Cameras (not advised), please run :
guvcview --cmos_camera=0 -d /dev/video0 -x 640x480 -r sdl -f yu12
 * if you need some help:


guvcview --help 



	Usage:
	   guvcview [OPTIONS]
	
	OPTIONS:
	-h,--help                             	:Print help
	-v,--version                          	:Print version
	-w,--verbosity=LEVEL                  	:Set Verbosity level (def: 0)
	-q,--cmos_camera=CAMERA               	:Set CMOS camera use (def: 1)
	-d,--device=DEVICE                    	:Set device name (def: /dev/video0)
	-c,--capture=METHOD                   	:Set capture method [read | mmap (def)]
	-b,--disable_libv4l2                  	:disable calls to libv4l2
	-x,--resolution=WIDTHxHEIGHT          	:Request resolution (e.g 640x480)
	-f,--format=FOURCC                    	:Request format (e.g MJPG)
	-r,--render=RENDER_API                	:Select render API (e.g none; sdl)
	-m,--render_window=RENDER_WINDOW_FLAGS	:Set render window flags (e.g none; full; max)
	-a,--audio=AUDIO_API                  	:Select audio API (e.g none; port; pulse)
	-k,--audio_device=AUDIO_DEVICE        	:Select audio device index for selected api (0..N)
	-g,--gui=GUI_API                      	:Select GUI API (e.g none; gtk3)
	-o,--audio_codec=CODEC                	:Audio codec [pcm mp2 mp3 aac ac3 vorb]
	-u,--video_codec=CODEC                	:Video codec [raw mjpg mpeg flv1 wmv1 mpg2 mp43 dx50 h264 vp80 theo]
	-p,--profile=FILENAME                 	:load control profile
	-j,--video=FILENAME                   	:filename for captured video)
	-i,--image=FILENAME                   	:filename for captured image)
	-y,--video_timer=TIME_IN_SEC          	:time (double) in sec. for video capture)
	-t,--photo_timer=TIME_IN_SEC          	:time (double) in sec. between captured photos)
	-n,--photo_total=TOTAL                	:total number of captured photos)
	-z,--control_panel                    	:Start in control panel mode


Basic Configuration
===================
Dependencies:
-------------

Guvcview depends on the following:
	 - intltool,
	 - autotools, 
	 - libsdl2 or libsdl, 
	 - libgtk-3, 
	 - portaudio19, 
	 - libpng, 
	 - libavcodec, 
	 - libavutil, 
	 - libv4l, 
	 - libudev,
	 - libusb-1.0,
	 - libpulse (optional)
	 - libgsl (optional)

On most distributions you can just install the development 
packages:

	intltool, autotools-dev, libsdl2-dev, libgtk-3-dev, 
	portaudio19-dev, libpng12-dev, libavcodec-dev, libavutil-dev,
	libv4l-dev, libudev-dev, libusb-1.0-0-dev, libpulse-dev, libgsl-dev

GUVCVIEW Author: Paulo Assis (https://sourceforge.net/p/guvcview/)

Build configuration:
--------------------
(./bootstrap.sh; ./configure)

The configure script is generated from configure.ac by autoconf,
the helper script ./bootstrap.sh can be used for this, it will also
run the generated configure with the command line options passed.
After configuration a simple 'make && make install' will build and
install guvcview and all the associated data files.

Data Files:
------------
(language files; image files; gnome menu entry)

guvcview data files are stored by default to /usr/local/share
setting a different prefix (--prefix=BASEDIR) during configuration
will change the installation path to BASEDIR/share.

Built files, src/guvcview and data/gnome.desktop, are dependent 
on this path, so if a new prefix is set a make clean is required 
before issuing the make command. 

After running the configure script the normal, make && make install 
should build and install all the necessary files.    
    
 
guvcview bin:
-------------
(guvcview)

The binarie file installs to the standart location,
/usr/local/bin, to change the install path, configure
must be executed with --prefix=DIR set, this will cause
the bin file to be installed in DIR/bin, make sure 
DIR/bin is set in your PATH variable, or the gnome 
menu entry will fail.

guvcview libraries:
-------------------
(libgviewv4l2core, libgviewrender, libgviewaudio, libgviewencoder)

The core functionality of guvcview is now split into 4 libraries
these will install to ${prefix}/lib and development headers to
${prefix}/include/guvcview-2/libname. 
pkg-config should be use to determine the compile flags.


guvcview.desktop:
-----------------

(data/guvcview.desktop)

The desktop file (gnome menu entry) is built from the
data/guvcview.desktop.in definition and is dependent on the 
configure --prefix setting, any changes to this, must 
be done in data/guvcview.desktop.in.

configuration files:
--------------------
(~/.config/guvcview2/video0)

The configuration file is saved into the $HOME dir when 
exiting guvcview. If a video device with index > 0,
e.g: /dev/video1 is used then the file stored will be
named ~/.config/guvcview2/video1

Executing guvcview
================== 

For instructions on the command line args 
execute "guvcview --help".
