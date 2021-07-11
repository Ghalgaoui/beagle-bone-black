# beagle-bone-black
BBB Utilities, U-Boot , Kernel and Rootfs
////////////////////////////////////////////////////////////////////////

- Bootloader has two main jobs:
	1- Init system to a basic level (Init DRAM controller ,etc)
	2- Load kernel to RAM.

- From NOR memory, the boot sequence becomes a complex, multi-stage procedure.
The details are very specific to each SOC (Generally follow 3 phases)

	1- Phase 01: ROM code
		- In the absence of reliable externel memory (SDCard, USB, etc), the code runs immediately after a reset power on
		has to be stored on-chip in the SOC (ROM Code) to loads the platform initialization firmware from flash memory.

	2- Phase 02: SPL (Second programm Loader) MLO in case of bbb
		initializes the DRAM controller and other system interfaces, so as to be able to load an EFI boot manager from the EFI System Partition (ESP) on a local disk, or
		from a network server via PXE boot.


http://www.denx.de/wiki/U-Boot



********************************************************************************************************************************************************************
sudo apt-get install automake bison chrpath flex g++ git gperf gawk libexpat1-dev libncurses5-dev libsdl1.2-dev libtool python2.7-dev texinfo

--------------------------------------------------------------------------------------------------------------------------------
Install toolchain
----------------------------------------------------------------------------------------------------------------------------------
$ git clone https://github.com/crosstool-ng/crosstool-ng.git
$ cd crosstool-ng
$ git checkout crosstool-ng-1.22.0
$ ./bootstrap
$ ./configure --enable-local
$ make
$ sudo make install

BBB contains an ARM Cortex A8 core and a
VFPv3 floating point unit.

$ ./ct-ng list-samples | grep cortex_a8
[L...]   arm-cortex_a8-linux-gnueabi

See default configs

kamel@kamel-ThinkPad-E590:~/bbb/crosstool-ng$ ./ct-ng show-arm-cortex_a8-linux-gnueabi
[L...]   arm-cortex_a8-linux-gnueabi
    Languages       : C,C++
    OS              : linux-4.20.8
    Binutils        : binutils-2.32
    Compiler        : gcc-8.3.0
    C library       : glibc-2.29
    Debug tools     : duma-2_5_15 gdb-8.2.1 ltrace-0.7.3 strace-4.26
    Companion libs  : expat-2.2.6 gettext-0.19.8.1 gmp-6.1.2 isl-0.20 libelf-0.8.13 libiconv-1.15 mpc-1.1.0 mpfr-4.0.2 ncurses-6.1
    Companion tools :


Select target config:
$ ./ct-ng arm-cortex_a8-linux-gnueabi

Review configuration
$ ./ct-ng menuconfig

There are two configuration changes that I would recommend you make at this point:
	1- In Paths and misc options, disable Render the toolchain read-only
	( CT_INSTALL_DIR_RO )
	2- In Target options | Floating point, select hardware (FPU) ( CT_ARCH_FLOAT_HW )

Build toolchain (Would take about half hour)

$ ./ct-ng build

Toolchain present here: ~/x-tools/arm-cortex_a8-linux-gnueabihf
-------------------------------------------------------------------------------------------------------------------------------------
