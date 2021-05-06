
IOTG Yocto BSP
==============

This README file contains information on building and booting
Yocto Project*-based BSP on Intel IoT platforms. Please see the
corresponding sections below for details.

Please check the known issues section, the latest copy of this
file from the git master branch may have newer/updated information
that was not known at the time of release.

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
    IV. Booting Up BSP
         a. Create a Bootable Image
         b. Booting up Intel Platform
    V. Known Issues

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


V. known issues
=================

* Pre-generated / Autogenerated secure boot keys (ALL)

In accordance to Intel Security Policies, the sample keys in release-40 through 60 and later has been removed.
This may cause build errors if bitbake is run immediately upon git checkout. A keyset needs to be generated
in order for the build to proceed.

For releases later than release-60_ehl-pv, an auto generated key will be produced if none is provided.
A pre-generated key may still be used by modifying the signing.inc configuration file.

Please contact your Intel representative on building the BSP with the pre-generated keys.

* LXC Download Link changed (release-60_ehl-pv and earlier)

The LXC download location has changed, causing build errors, to fix the error:
  In the file intel-embedded-system-enabling/meta-virtualization/recipes-containers/lxc/lxc_XXXXXX.bb
  change all mentions of http://linuxcontainers.org/downloads/${BPN}-${PV}.tar.gz to
  http://linuxcontainers.org/downloads/${BPN}/${BPN}-${PV}.tar.gz

This will resolve the download problem.

* Pregenerated UEFI Secure Boot key provider (secure-boot-certificates-pregenerated) causing shim build failures (release-64_cml-mr2)

The shim recipe assumes the Machine Owner Key and Database keys in DER encoded X509 format is provided, this is not the case
in secure-boot-certificates-pregenerated. This can be fixed by applying the following patch to meta-intel-ese-main directory:

Note: You may want to edit the file directly instead or download a copy of this file, since github markdown language display
interferes with tab spacings when shown in the browser.

```
diff --git a/recipes-bsp/shim/shim_git.bb b/recipes-bsp/shim/shim_git.bb
index dcae03a..3bf22b5 100644
--- a/recipes-bsp/shim/shim_git.bb
+++ b/recipes-bsp/shim/shim_git.bb
@@ -32,13 +32,17 @@ inherit gnu-efi
 do_configure[depends] += "virtual/secure-boot-certificates:do_deploy"
 do_configure_append() {
 	cp ${DEPLOY_DIR_IMAGE}/secure-boot-certificates/yocto.crt \
-	${DEPLOY_DIR_IMAGE}/secure-boot-certificates/yocto.cer \
 	${DEPLOY_DIR_IMAGE}/secure-boot-certificates/yocto.key \
 	${DEPLOY_DIR_IMAGE}/secure-boot-certificates/shim.crt \
 	${DEPLOY_DIR_IMAGE}/secure-boot-certificates/shim.key \
-	${DEPLOY_DIR_IMAGE}/secure-boot-certificates/shim.cer .
+	.
 	openssl pkcs12 -export -in shim.crt -inkey shim.key -out shim.p12 -passout pass:
 
+	# X509 DER form
+	for name in shim yocto; do
+		openssl x509 -outform DER -in "${name}.crt" -out "${name}.cer"
+	done
+
 	# ese sbat marker append, should really be in UTF-8 specifically
 	mkdir -p ${B}/data
 	echo 'shim.ese,1,ESE,${PN},${PV},https://github.com/intel/iotg-yocto-ese-main' > ${B}/data/sbat.ese.csv
```

This causes the shim recipe to no longer depend on the DER certificates, instead generate them from the PEM formatted X509
certs.
