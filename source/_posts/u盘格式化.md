title: linux 下U盘格式化命令
tags: linux
---
命令行原则：先卸载，后格式化。
假如U盘挂载在/media/disk上

卸载：
```
sudo umount /media/disk
```
可以用`sudo fdisk -l`查看U盘挂载位置
格式化：
```
sudo mkfs.vfat /dev/sdb
```
不同的格式对应不同的格式化命令：
```
mkfs mkfs.cramfs mkfs.ext3 mkfs.ext4dev mkfs.msdos mkfs.vfat
mkfs.bfs mkfs.ext2 mkfs.ext4 mkfs.minx mkfs.ntfs
```

