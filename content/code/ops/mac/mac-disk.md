
---
title: "Mac Disk"
---

## diskutil

```shell
# 硬盘的设备文件地址
diskutil list

diskutil unmountDisk /dev/disk2
# eject the protable device
diskutil eject /dev/disk2

sudo dd if=<outfile.dmg> of=/dev/rdisk2 bs=1m
```

### iso

```shell
# extract a ios file
hdiutil convert -format UDRW -o <outfile> <path_to_your_iso_file>
# mount a ios file
hdiutil attach <path_to_your_iso_file>
hdiutil mount <path_to_your_iso_file> -mountroot /Volumns/ios
hdiutil detach /Volumns/ios
```

## FUSE

```shell
brew update
# 安装ntfs-3g包依赖的osxfuse
brew cask install osxfuse
# brew install homebrew/fuse/ext4fuse
brew install ext4fuse

sudo mkdir /Volumes/ext4disk
sudo ext4fuse /dev/disk2 /Volumes/ext4disk

# brew install homebrew/fuse/ntfs-3g
brew install ntfs-3g

# 如果系统自动挂载则在Finder推出
# 修复NTFS分区
sudo ntfsfix /dev/disk2s4
# sudo ntfsfix -d /dev/disk2s1
sudo mkdir /Volumes/HD1
sudo mount -t ntfs -o rw,auto,nobrowse /dev/disk2s4 /Volumes/HD1

# sudo mkdir /Volumes/HD2 && sudo mount -t ntfs -o rw,auto,nobrowse /dev/disk2s1 /Volumes/HD2
```

### NTFS

```shell
mount -t ntfs -o rw,auto,nobrowse /dev/disk4s2 /Volumes/Udisk/
sudo ln -s /Volumes/Udisk/ ~/Desktop/Udisk

sudo /usr/local/bin/ntfs-3g -olocal -oallow_other /dev/disk2s1 /Volumes/NTFS 

sudo mv "/Volumes/Macintosh HD/sbin/mount_ntfs" "/Volumes/Macintosh HD/sbin/mount_ntfs.bak"
sudo ln -s /usr/local/sbin/mount_ntfs "/Volumes/Macintosh HD/sbin/mount_ntfs"
```

-t ntfs # 执行要挂载的分区文件系统格式
-o # 执行挂载的选项
rw # read-write，以读写的方式挂载
auto # 自动检测文件系统,此参数可以省略
nobrowse # 这个选项非常重要，因为这选项指明了在finder里不显示这个分区，只有打开了这个选项才能将磁盘以读写的方式进行挂载
/dev/disk4s2 # 要挂载的分区，也就是我们在mount命令中看到的盘符
/Volumes/Udisk/ # 挂载点，需要存在

### NTFS

```shell
brew cask install osxfuse
brew install ntfs-3g
sudo su -
# Operation not permitted
mv /sbin/mount_ntfs /sbin/mount_ntfs.bak
ln -s /usr/local/sbin/mount_ntfs /sbin/mount_ntfs
```

```shell
# failed to modify system integrity configuration. This tool needs to be executed from the Recovery OS.
# command+R  to enter Recovery mode
# disable System Integrity Protection
csrutil disable

# after reboot 
sudo su -
# Read-only file system
# mount a Read-Write file system
mount -uw /
```

### vdi

```shell
hexdump -C win10.vdi | head -10

ln -s win10.vdi win10.img

# 00100000   0x800
# 0x200000 (2097152) / 0x200 (512) = 0x1000 (4096)
hdiutil attach -agent hdid -section 0x1000 -readonly win10.img
```

```shell
ln -s somevdi someimg
# echo /dev/disk2
sudo hdid -nomount someimg
hdiutil detach /dev/disk2

hexdump -C -s 0x158 -n 4 ubuntu.vdi
echo "obase=16; ibase=16; 5000 / 200" | bc
hdid -section 0x28 -nomount ubuntu.img
```
