
IOTG Yocto BSP
==============

This README file contains information on building and booting
Yocto Project*-based BSP on Intel IoT platforms. Please see the
corresponding sections below for details.

Notes
=====

Recent changes in the BSP:

    a. Pre-generated keys for Secure Boot.
       Please contact your Intel representative on building the BSP with the pre-generated keys. 

Table of Contents
=================

    I. Overview
    II. Yocto Project Compatibility
    III. Getting Started with BSP
         a. Yocto Project*-based Pre-requisite
         b. Obtain Repo Sources
         c. Build Steps
    V. Booting Up BSP
         a. Create a Bootable Image
         b. Booting up Intel Platform

I. Overview
===========

This is the location of Intel IoTG maintained Yocto Project*-based BSP.
For details of the release note documentation from Intel Resource and
Design Centre by searching its document number.

    https://www.intel.com/content/www/us/en/design/resource-design-center.html


II. Yocto Project Compatibility
===============================

The BSPs contained in this layer are compatible with the Yocto Project
as per the requirements listed here:

    a. Dunfell
    b. Gatesgarth


III. Getting Started with BSP
====================================

a. Yocto Project*-based Pre-Requisite
-------------------------------------

The following section consists of a one-time setup for host and its dependencies.

This is a one-time setup for user host dependencies.
Please skip this steps if this was not your first time building a
Yocto-Project*-based BSP. As a user you will need to install necessary
toolchain to build the Yocto Project*-based BSP.

    $ sudo apt-get -y install socat gawk wget git-core diffstat unzip texinfo \
       build-essential chrpath libsdl1.2-dev xterm libncurses5-dev patchutils 
    $ sudo apt-get install connect-proxy

b. Obtain Repo Sources
----------------------

Create a bin/ directory in your home directory and included in your path.

    $ mkdir ~/bin
    $ PATH=~/bin:$PATH

Get repo source and make it executable.

    $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    $ chmod a+x ~/bin/repo


c. Build Steps
--------------

Make sure to have "repo" and the toolchain dependencies are set before proceed.
All Yocto-Project*-based BSP is access contolled repositories.
Please contact your Intel representatives for release tag.

Make a new directory.
    
    $ mkdir <work_dir>
    $ cd <work_dir>

Git clone the repo manifest. This manifest will help user to clone all 
the required repositories to create the base bsp.

    $ repo init -u https://github.com/intel/iotg-yocto-ese-manifest.git -b refs/heads/master -g all
   
or depend on your release;
   
    $ repo init -u https://github.com/intel/iotg-yocto-ese-manifest.git -b refs/tags/release-15 -g all
 
Pull the repository meta-layers (-j4 for simultaneous downloads, increase for more).

    $ repo sync -c -j8 --force-sync

Make a branch.

    $ repo forall managed/* -c git branch -f BUILD HEAD

Start build process.

    $ cd <work_dir>/build
    $ . ../intel-embedded-system-enabling/oe-init-build-env .

Run bitbake compilation.

    $ bitbake mc:x86:core-image-sato-sdk

Additional notes.

a. To change provided kernel to your preferred kernel
   
    $ cd <work_dir>/build/
    $ vi conf/multiconfig/x86.conf

   Substitute "linux-intel-mainline" with preferred kernel
    PREFERRED_PROVIDER_virtual/kernel = "linux-intel-mainline"

IE: PREFERRED_PROVIDER_virtual/kernel = "linux-vanilla"

Comment out this line.
   KERNEL_PACKAGE_NAME_pn-linux-intel-mainline = "kernel"

   Continue with steps 6.


IV. Booting Up BSP
=================

Intel recommends using "bmaptool" to prepare a bootable image with the
USB* flash drive. 

a. Create a Bootable Image
--------------------------

Download the latest bmaptool release from 
https://github.com/intel/bmap-tools/releases/ into the Ubuntu* host 
system, where you build the Yocto Projectbased image.

    $ git clone https://github.com/intel/bmap-tools.git

Ensure that Python* module six is installed on the system.

Insert the USB flash drive and ensure all partitions of the target device
(the USB flash drive in this case) are unmounted.

Run the following command (assume the USB flash drive is using /dev/sdc)
to generate a bootable USB flash drive.

    $ sudo ./bmap-tools/bmaptool copy --bmap <path>/core-image-sato-xxx-date>.wic.bmap <path>/core-image-sato-<date>.wic /dev/sdc

b. Booting Up Intel Platform
----------------------------

This is a general steps on booting your Intel platform.
Please contact you Intel representative for more information.

Insert a USB* flash drive into the Intel supported platform
   and ensure the IFWI has been installed.

Boot up the Intel platform by pressing the power button.
   Press F2 if you need to enter the BIOS menu for configuration
   or to select the boot option.

At the log in screen, type “root” to log in. No password is
   required.
