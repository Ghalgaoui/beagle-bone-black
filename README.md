# beagle-bone-black
## BBB Utilities, U-Boot , Kernel and Rootfs

### Boot sequence
	Bootloader has two main jobs:
		1- Init system to a basic level (Init DRAM controller ,etc)
		2- Load kernel to RAM.

	From NOR memory, the boot sequence becomes a complex, multi-stage procedure.
	The details are very specific to each SOC (Generally follow 3 phases)

		1. Phase 01: ROM code
			- ROM code is capable of loading a small chunk of code from one or serval pre-programmed locations into the SRAM.
			- It try to load code from few first pages of NAND flash memory, or from first sectors of an MMC device (eMMC or an SD card) 
				or from a file named MLO on first partition of an MMC device.
			- In SoCs where the SRAM is not enough to load a full bootloader like u-boot or barebox, there is an intermediate loader called SPL (Sec Prog Loader).
			==> At the end of ROM code phase, the SPL is present in the SRAM and the ROM code jumps to the beginning of that code.

		2. Phase 02: Secondary Program Loader SPL
			- The SPL must set up the memory controller and other essential parts of the system preparatory to loading the Tertiary Program Loader (TPL) into DRAM.
			- At the end of the second phase, the TPL is present in DRAM, and the SPL can make a jump to that area.

		3. Phase 03: Tertiary Program Loader (TPL)
			- Now, at last, we are running a full bootloader, such as U-Boot or BareBox.
			- TPL loads kernel + fdt + initramfs into DRAM.
			- At the end of the third phase, there is a kernel in memory, waiting to be started.
			- Embedded bootloader disappear from memory one kernel is running.

![Alt text](./boot.jpg?raw=true "Boot 3 Phase architecture")


[u-boot wiki from denx](http://www.denx.de/wiki/U-Boot)


#### Booting with EFI






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
