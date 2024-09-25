
IOTG Yocto BSP
==============

This README file contains information on building and booting
Yocto Project*-based BSP on Intel IoT platforms. Please see the
corresponding sections below for details.

Notes
=====

This is a snapshot of the latest Yocto Project BSP on Intel IoT platforms that was last
successfully validated. Older Intel platforms are supported but not fully validated. It
is recommended to use newer snapshots if possible to resolve CVEs discovered in older
snapshots. CVEs in Older snapshots are not retroactively fixed.

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

    a. Kirkstone

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
      libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev \
      screen git zstd liblz4-tool connect-proxy


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

    $ repo init -u https://github.com/intel/iotg-yocto-ese-manifest.git -b refs/heads/master -g all
   
or depend on your release;
   
    $ repo init -u https://github.com/intel/iotg-yocto-ese-manifest.git -b refs/tags/staging/release-150 -g all
 
Pull the repository meta-layers (-j4 for simultaneous downloads, increase for more).

    $ repo sync -c -j8 --force-sync

Make a branch.

    $ repo forall managed/* -c git branch -f BUILD HEAD

Start build process.

    $ cd <work_dir>/build
    $ . ../intel-embedded-system-enabling-pre/oe-init-build-env .

Run bitbake compilation.

a. To set LTS Kernel v6.6 as a default kernel

    $ bitbake mc:x86-2023:core-image-sato-sdk

b. To set LTS RT v6.6 as a default kernel

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
https://github.com/yoctoproject/bmaptool into the Ubuntu* host
system, where you build the Yocto Project based image.

    $ sudo apt-get install bmap-tools

Insert the USB flash drive and ensure all partitions of the target device
(the USB flash drive in this case) are unmounted.

Run the following command (assume the USB flash drive is using /dev/sdc)
to generate a bootable USB flash drive.

    $ sudo bmaptool copy --bmap <path>/core-image-sato-xxx-date>.wic.bmap <path>/core-image-sato-<date>.wic /dev/sdc

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
