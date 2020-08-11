What's this?
------------

This package allows you to use the USB Audio interfaces from M-Audio/
Midiman with Linux.  It sets up udev rules to load the firmware into the
device.

Supported devices:
- MobilePre
- Ozone
- Sonica
- Transit

The project page is <http://usb-midi-fw.sourceforge.net/>.

Updated Firmware
------------------
I have added the updated transit firmware, the ma006101.bin, and created a branch (ma006101-mod) to test with.

I'm not sure if there are more updated firmwares for the supported hardware.

Patches
------------
The following patches are already applied to this source tree (from the debian package source):
    42-madfuload-rules.patch
    configure.ac.patch
    fix-64-bit-implicit-declarations.patch

Prerequisites
-------------

- Linux 2.6.8 or later.  Earlier kernels have a bug in the USB reset
  code which will prevent the loader from working correctly.
- udev version 057 or later


Installing
----------

```
Obtaining the udev version info has changed since the configure script was written, so to get around it edit the configure file
and change (line 2811) 

udev_ver=`udevinfo -V | awk '{ print $3 }'`

to 

udev_ver=`udevadm info --v`

or

udev_ver=154

or

udev_ver=$(udevadm --version)

```

1) Run './configure'.

2) Run 'make'.

3) As root, run 'make install'.


Uninstalling
------------

As root, run 'make uninstall'.

Running
-------

The udev rules cause the firmware to be automatically loaded when the
device is connected to the computer.

```
The M-Audio Transit (and also older Edirol devices like the UA-EX1 and UA-4FX etc with advanced mode) can't do 96000hz duplex, so only record or playback can be chosen at any one time.

Assuming the M-Audio Transit is hw:1 (cat /proc/asound/cards)

jackd --realtime -d alsa -P -d hw:1 -p256 -n2 -r96000 // playback

jackd --realtime -d alsa -C -d hw:1 -p256 -n2 -r96000 // record

and the M-Audio Transit needs a ~/.asoundrc file containing

pcm.!default
{
type plug
slave sl3
}

ctl.!default
{
type hw
card 1
}

pcm_slave.sl3
{
pcm "hw:1,0"
format S24_3LE
channels 2
rate 96000
}

pcm.96000
{
type plug
slave sl3
}

```

Contact
-------

For help, discussion or feedback, send a mail to the mailing list at
<usb-midi-fw-user@lists.sourceforge.net>.

written by Clemens Ladisch <clemens@ladisch.de>
