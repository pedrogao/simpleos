# bootloader

开机工作：

- 开机后 CPU 的指令寄存器 ip 被强置为地址 0xFFFF0，这一地址被映射到 BIOS 固件上的代码，这就是计算机开机后的第一条指令的地址；
- CPU 开始执行 BIOS 上的代码，这一部分主要是硬件输入输出设备相关的检查，以及建立一个最初的中断向量表，目前不必深究；
- BIOS 代码最后阶段的工作，就是检查启动盘上的 mbr 分区，所谓 mbr 分区就是磁盘上的第一个 512B 内容，又叫引导分区；BIOS 会对这 512B 做一个检查：它的最后 2 个字节必须是两个 magic number：0x55 和 0xaa，否则它就不是一个合法的启动盘；
- 检查通过后，BIOS 将这 512B 加载到内存 0x7C00 处，到 0x7E00 为止，然后指令跳转到 0x7C00 开始执行；至此 BIOS 退出舞台；

[mbr](../boot/mbr.s) -> bootloader -> kernel

制作磁盘：

```sh
$ nasm -o mbr ./boot/mbr.s
$ dd if=mbr of=boot.img bs=512 count=1 seek=0 conv=notrunc
$ bochs -f bochsrc.txt
```

查看 boot.img：

```sh
$ xxd -l 512 ./boot.img
```

查看反汇编代码，比如查看 bootloader 处的指令：

```s
<bochs:4> u /20 0x7c00
# 或者
<bochs:3> u 0x7c00 0x7cff
```

## 参考资料

- [BIOS 启动到实模式](https://segmentfault.com/a/1190000040131294)
- [Bootloader basics](https://notes.eatonphil.com/bootloader-basics.html)
- [Bochs 调试常用命令](https://petpwiuta.github.io/2020/05/09/Bochs%E8%B0%83%E8%AF%95%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/)
