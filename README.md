TGL Yocto BSP Pre-Alpha Release Note

This Release Note is divided into 3 section.

A. Toolchain dependencies
B. Repo manifest setup
C. Build steps


A. Toolchain Dependencies

1. Install necessary toolchain
	$ sudo apt-get -y install socat gawk wget git-core diffstat unzip texinfo build-essential chrpath libsdl1.2-dev xterm libncurses5-dev patchutils
	$ sudo apt-get install connect-proxy

B. Obtain repo sources

1. Create a bin/ directory in your home directory and included in your path.
	$ mkdir ~/bin
	$ PATH=~/bin:$PATH

2. Get repo source and make it executable.
	$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
	$ chmod a+x ~/bin/repo

C. Build Steps

# Make sure to have "repo" and the toolchain dependencies are set before proceed.

1. Make a new directory.
	$ mkdir <work_dir>
	$ cd <work_dir>

2. Git clone the repo manifest.
	$ repo init -u https://github.com/syazaath/test-manifest.git -b refs/heads/master -g all

	This manifest will help user to clone all the required repositories to create the base bsp.

3. Pull the repository meta-layers (-j4 for simultaneous downloads, increase for more).
	$ repo sync -c -j8 --force-sync

# To build BSP without TCC and Turtle-Creek continue to step #8.
# To build BSP with TCC and Turtle Creek continue to step #4.

4. Download these tarball from artifactory.
	Link:

	This proprietery tarball has two components:
		a. The proprietery recipes (Turtle Creek and TCC)
		b. The proprietery binaries

5. Extract tarball and copy in this directory :
	ie: <work_dir>/intel-embedded-system-enabling-pre/meta-intel-embedded-system-enabling-pre/

	$ tar -xvjf tgl-proprietery.tar.bz2

	i. Copy meta-intel-ese-proprietary-pre to your work_dir.
	$ cp -r meta-intel-ese-proprietary-pre <work_dri>/intel-embedded-system-enabling-pre/meta-intel-embedded-system-enabling-pre \
	/meta-intel-ese-proprietary-pre

	i. Copy propritery content to your work_dir.
	$ cp -r proprietary <work_dri>/intel-embedded-system-enabling-pre/meta-intel-embedded-system-enabling-pre \
	/meta-intel-ese-proprietary-pre/

	This are the structure how it suppose to look like in the BSP.

	|-<work_dir>/intel-embedded-system-enabling-pre/meta-intel-embedded-system-enabling-pre
					|-meta-intel-ese-proprietary-pre
						|-meta-intel-ese-manageability
						|-meta-intel-tcc
						|-proprietary

6. Edit the local.conf.
	$ cd <work_dir>/build
	$ vi conf/local.conf

	# Copy the below item to conf/local.conf

	# Managebility proprietery content
	IMAGE_INSTALL_append = " turtle-creek"
	TURTLE_CREEK_TGZ_PATH = "${TOPDIR}/../${METALAYER_PREFIX}meta-${METALAYER_PREFIX}meta-intel-ese-proprietary-pre/proprietary/bin/turtle-creek"
	# END Managebility proprietery content

	# TCCSDK
	BS_LOCATION = "${TOPDIR}/../${METALAYER_PREFIX}meta-${METALAYER_PREFIX}meta-intel-ese-proprietary-pre/proprietary/bin/tcc"
	PREFERRED_VERSION_dpdk = "17.11%"
	IMAGE_INSTALL_append = " \
	stress-ng perf numactl tccsdk-bin \
	util-linux util-linux-lscpu util-linux-taskset"
	# END TCCSDK

7. Add proprietery layers in the bblayers.conf.
	$ cd <work_dir>/build
	$ vi conf/bblayers.conf

	# Copy the below item to conf/bblayers.conf
	{TOPDIR}/../${METALAYER_PREFIX}meta-${METALAYER_PREFIX}meta-intel-ese-proprietary-pre/meta-intel-ese-manageability \
	{TOPDIR}/../${METALAYER_PREFIX}meta-${METALAYER_PREFIX}meta-intel-ese-proprietary-pre/meta-intel-tcc \

8.  To start build process.
	$ cd <work_dir>/build
	$ . ../intel-embedded-system-enabling-pre/oe-init-build-env .
	$ bitbake multiconfig:x86:core-image-sato-sdk

# If you wish to change your kernel configuration.
	$ cd <work_dir>/build/conf/multiconfig
	$ vi x86.conf

#Comment out this line
KERNEL_PACKAGE_NAME_pn-linux-intel-mainline = "kernel"

ie:#KERNEL_PACKAGE_NAME_pn-linux-intel-mainline = "kernel"

#Replace kernel provider with your preferences
PREFERRED_PROVIDER_virtual/kernel = "linux-intel-mainline"

ie:PREFERRED_PROVIDER_virtual/kernel = "linux-intel-rt"

