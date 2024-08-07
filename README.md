
IOTG Yocto BSP
==============

This README file contains information on building and booting
Yocto Project*-based BSP on Intel IoT platforms. Please see the
corresponding sections below for details.

Notes
=====

There are recent changes in the BSP:

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
    b. Hardknott
    c. Kirkstone

Note: Yocto meta-* layer, kirkstone branch in not backward compatible with
      meta-* layer dunfell or hardknott branch. All tags starting from
      staging/release-99 onwards are based on Yocto Project Kirkstone release.


III. Getting Started with BSP
====================================

a. Yocto Project*-based Pre-Requisite
-------------------------------------

The following section consists of a one-time setup for host and its dependencies.

This is a one-time setup for user host dependencies.
Please skip this steps if this was not your first time building a
Yocto-Project*-based BSP. As a user you will need to install necessary
toolchain to build the Yocto Project*-based BSP.

    $ sudo apt-get install gawk wget diffstat unzip texinfo build-essential \
      chrpath socat cpio python3 python3-pip python3-pexpect xz-utils \
      debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa \
      libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev
    $ sudo apt-get install connect-proxy

    Dunfell/Hardknott build

    $ sudo apt-get install git-core gcc-multilib

    Kirkstone build

    $ sudo apt-get install git gcc zstd liblz4-tool


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
Please contact your Intel representatives for access.

Make a new directory.
    
    $ mkdir <work_dir>
    $ cd <work_dir>

Git clone the repo manifest. This manifest will help user to clone all 
the required repositories to create the base bsp.

    $ repo init -u git@github.com:otcshare/staging-iotg-yocto-ese-manifest.git -b refs/heads/master -g all
   
or depend on your release;
   
    $ repo init -u git@github.com:otcshare/staging-iotg-yocto-ese-manifest.git -b refs/tags/staging/release-106 -g all
 
Pull the repository meta-layers (-j4 for simultaneous downloads, increase for more).

    $ repo sync -c -j8 --force-sync

Make a branch.

    $ repo forall managed/* -c git branch -f BUILD HEAD

Start build process.

    $ cd <work_dir>/build
    $ . ../intel-embedded-system-enabling-pre/oe-init-build-env .

Run bitbake compilation.

a. To set LTS kernel v5.4 as a default kernel

    $ bitbake mc:x86:core-image-sato-sdk

b. To set LTS RT v5.4 as a default kernel

    $ bitbake mc:x86-rt:core-image-sato-sdk

c. To set Mainline Tracking Kernel v5.10 as a default kernel

    $ bitbake mc:x86-mlt:core-image-sato-sdk

d. To set LTS Kernel v5.10 as a default kernel
 
    $ bitbake mc:x86-2020:core-image-sato-sdk

e. To set LTS RT v5.10 as a default kernel

    $ bitbake mc:x86-rt-2020:core-image-sato-sdk

f. To set LTS Kernel v5.15 as a default kernel

    $ bitbake mc:x86-2021:core-image-sato-sdk

g. To set LTS RT v5.15 as a default kernel

    $ bitbake mc:x86-rt-2021:core-image-sato-sdk

h. To set LTS Kernel v6.1 as a default kernel

    $ bitbake mc:x86-2022:core-image-sato-sdk

i. To set LTS RT v6.1 as a default kernel

    $ bitbake mc:x86-rt-2022:core-image-sato-sdk

j. To set LTS Kernel v6.6 as a default kernel

    $ bitbake mc:x86-2023:core-image-sato-sdk

k. To set LTS RT v6.6 as a default kernel

    $ bitbake mc:x86-rt-2023:core-image-sato-sdk


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
https://github.com/intel/bmaptools/releases into the Ubuntu* host 
system, where you build the Yocto Projectbased image.

    $ curl -Lo bmaptool https://github.com/01org/bmaptools/releases/download/v3.4/bmaptool && chmod +x bmaptool

Ensure that Python* module six is installed on the system.

Insert the USB flash drive and ensure all partitions of the target device
(the USB flash drive in this case) are unmounted.

Run the following command (assume the USB flash drive is using /dev/sdc)
to generate a bootable USB flash drive.

    $ sudo ./bmaptool copy --bmap <path>/core-image-sato-xxx-date>.wic.bmap <path>/core-image-sato-<date>.wic /dev/sdc

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


V. Maintainer
============

jonathan.yong@intel.com
Kartikey.Rameshbhai.Parmar@intel.com
preeti.sachan@intel.com
