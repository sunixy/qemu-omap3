

# Caution #
This page is a little outdated. I am too busy to contiue this project. The guy at maemo(now meego) has made great improvement in qemu omap3 emulation. Go to [this page](http://meego.gitorious.org/qemu-maemo) for more information.

# Introduction #

This article will tell you how to use qemu-omap3 v0.01.

# Requirement #

You must install the required package before using qemu-omap3.
  * gcc3
  * libsdl

# Download The Source Code #
```
wget http://qemu-omap3.googlecode.com/files/qemu-omap3-v0.01.tar.bz2
tar jxvf qemu-omap3-v0.01.tar.bz2
cd qemu-omap3 
```

# Build qemu-omap3 #
```
./configure --target-list=arm-softmmu
make
```
qemu-system-arm will be generated in directory qemu-omap3/arm-softmmu.

# Download u-boot/kernel/rootfs #
```
cd arm-softmmu
wget http://qemu-omap3.googlecode.com/files/image-v0.01.tar.bz2
tar jxvf image-v0.01.tar.bz2
wget http://beagleboard.googlecode.com/files/rd-ext2-8M.bin
```

# Generate nand flash image #

A script bb\_nandflash.sh is used to put xloader/u-boot/linux kernel together to generate nand flash image.

Caution: bb\_nandflash.sh is not compatible with dash. If you are using ubuntu, please do not install dash as /bin/sh or edit the bb\_nandflash.sh. You can try **ANY** of these.
```
1. dpkg-reconfigure dash
choose Install dash as /bin/sh? with NO.
```
```
2. change bb_nandflash.sh
#! /bin/sh
->
#! /bin/bash
```

Assume you are in qemu-omap3/arm-softmmu.
```
cp ../bb_nandflash.sh .
cp ../bb_nandflash_ecc .
./bb_nandflash.sh x-load.bin.ift beagle-nand.bin x-loader
./bb_nandflash.sh u-boot.bin beagle-nand.bin u-boot
./bb_nandflash.sh uImage beagle-nand.bin kernel
./bb_nandflash.sh rd-ext2-8M.bin  beagle-nand.bin  rootfs
./bb_nandflash_ecc beagle-nand.bin 0x0 0xe80000
```

# Run qemu-omap3 #
./qemu-system-arm -M beagle -mtdblock beagle-nand.bin

Using Ctrl-Alt-3 to switch to beagle board serial port. You can see x-loader and u-boot booting message. When u-boot command line appears, type the following commands.

```
OMAP3 beagleboard.org # nand read 0x80000000 0x280000 0x400000
NAND read: device 0 offset 0x280000, size 0x400000
4194304 bytes read: OK
OMAP3 beagleboard.org # nand read 0x81600000 0x680000 0x800000
NAND read: device 0 offset 0x680000, size 0x800000
8388608 bytes read: OK
OMAP3 beagleboard.org # setenv bootargs 'console=ttyS2,115200n8 ramdisk_size=8192 root=/dev/ram0 rw rootfstype=ext2 initrd=0x81600000,8M nohz=0ff'
OMAP3 beagleboard.org # bootm 0x80000000
```

Linux will boot with some debug information of qemu-omap3. Enjoy it.

[http://lh4.ggpht.com/\_vf\_uV7bji2A/SUYcM3m6jLI/AAAAAAAAANo/9nKq87MosAg/s400/bb2.jpg ](http://picasaweb.google.com/lh/photo/SG8C2_rqSfuuH_xMvqTiLg)