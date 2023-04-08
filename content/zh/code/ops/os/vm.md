
---
title: "VM"
---

## VBox

```shell
VBoxManage import --vsys 0 --vmname myvm some.ova
VBoxManage export myvm -o some.ova

# start
VBoxHeadless -startvm myvm --vrdp on
VBoxManage startvm myvm --type headless
# VBoxManage stopvm myvm

VBoxManage list vms
VBoxManage list runningvms

# stop
VBoxManage controlvm myvm savestate
VBoxManage controlvm myvm acpipowerbutton
VBoxManage controlvm myvm poweroff

# remote
VBoxManage modifyvm myvm --vrde on
VBoxManage modifyvm myvm --vrdeport 3389
VBoxManage modifyvm myvm --vrdeextpack default
# VBoxManage modifyvm myvm --vrdeaddress 127.0.0.2
mstsc 1.2.3.4:3389
rdesktop -a 16 -N 1.2.3.4:3389
```

### issue

#### VT-x is not available (VERR_VMX_NO_VMX)
```
egrep -q 'vmx|svm' /proc/cpuinfo && echo yes || echo no
```

1. Enable VT-X From BIOS
Intel Virtualization Technology or the SVM mode in AMD computers

2. Disable Hyper-V and Memory Integrity for Windows
```cmd
bcdedit /set hypervisorlaunchtype off
dism.exe /Online /Disable-Feature:Microsoft-Hyper-V
```
Windows Security - Device Security - Memory Integrity

## VMware

| Edition           | Active Code                   |
| ----------------- | ----------------------------- |
| Fusion Pro 13.0.0 | 4C21U-2KK9Q-M8130-4V2QH-CF810 |
|                   |                               |

## UTM

https://mac.getutm.app

https://mac.getutm.app/gallery/debian-10-4-minimal
