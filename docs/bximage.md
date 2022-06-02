# bximage 软盘制作

1. 通过 bximage 制作 1.44M 软盘，boot.img：

```sh
$ bximage
========================================================================
                                bximage
  Disk Image Creation / Conversion / Resize and Commit Tool for Bochs
         $Id: bximage.cc 14091 2021-01-30 17:37:42Z sshwarts $
========================================================================

1. Create new floppy or hard disk image
2. Convert hard disk image to other format (mode)
3. Resize hard disk image
4. Commit 'undoable' redolog to base image
5. Disk image info

0. Quit

Please choose one [0] 1

Create image

Do you want to create a floppy disk image or a hard disk image?
Please type hd or fd. [hd] fd

Choose the size of floppy disk image to create.
Please type 160k, 180k, 320k, 360k, 720k, 1.2M, 1.44M, 1.68M, 1.72M, or 2.88M.
 [1.44M]

What should be the name of the image?
[a.img] boot.img

Creating floppy image 'boot.img' with 2880 sectors

The following line should appear in your bochsrc:
  floppya: image="boot.img", status=inserted
```

2. 编译 boot，然后通过 `dd` 写入 boot.img 中，相当于将 bootloader 写入磁盘：

```sh
$ nasm boot.s -o boot.bin
$ dd if=boot.bin of=boot.img bs=512 count=1 conv=notrunc
```

3. 配置 bichsrc.txt 配置文件：

```
# for MAC os
megs: 32
romimage: file=/usr/local/Cellar/bochs/2.7/share/bochs/BIOS-bochs-latest
vgaromimage: file=/usr/local/Cellar/bochs/2.7/share/bochs/VGABIOS-lgpl-latest
floppya: 1_44=boot.img, status=inserted
boot: floppy
log: run.log
mouse: enabled=0
display_library: sdl2
```

4. 运行：

```sh
$ bochs -f bochsrc.txt
```
