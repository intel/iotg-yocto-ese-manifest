
IOTG ESE Yocto BSP Pre-Alpha Release Note

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

Make sure to have "repo" and the toolchain dependencies are set before proceed.

1. Make a new directory.
$ mkdir <work_dir>
$ cd <work_dir>

2. Git clone the repo manifest. This manifest will help user to clone all the required repositories to create the base bsp.
$ repo init -u https://github.com/otcshare/IOTG-Yocto-ESE-Manifest.git -b refs/heads/master -g all

3. Pull the repository meta-layers (-j4 for simultaneous downloads, increase for more).
$ repo sync -c -j8 --force-sync

4. To start build process
$ cd <work_dir>/build
$ . ../intel-embedded-system-enabling-pre/oe-init-build-env .

5. Run bitbake compilation
$ bitbake multiconfig:x86:core-image-sato-sdk

To change provided kernel to your preferred kernel
$ cd <work_dir>/build/
$ vi conf/multiconfig/x86.conf

Substitute "linux-intel-mainline" with preferred kernel
PREFERRED_PROVIDER_virtual/kernel = "linux-intel-mainline"

ie: PREFERRED_PROVIDER_virtual/kernel = "linux-vanilla"

Comment out this line.
KERNEL_PACKAGE_NAME_pn-linux-intel-mainline = "kernel"

Continue with steps 5.
