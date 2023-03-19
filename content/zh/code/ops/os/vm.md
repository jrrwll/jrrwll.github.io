
---
title: "VM"
---

## VBox

```shell
# start
VBoxHeadless -startvm vm_name --vrdp on
VBoxManage startvm vm_name --type headless
# VBoxManage stopvm vm_name

VBoxManage list vms
VBoxManage list runningvms

# stop
VBoxManage controlvm vm_name savestate
VBoxManage controlvm vm_name acpipowerbutton
VBoxManage controlvm vm_name poweroff

# remote
VBoxManage modifyvm vm_name --vrde on
VBoxManage modifyvm vm_name --vrdeport 3389
VBoxManage modifyvm vm_name --vrdeextpack default
# VBoxManage modifyvm vm_name --vrdeaddress 127.0.0.2
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

## UTM

https://mac.getutm.app

https://mac.getutm.app/gallery/debian-10-4-minimal
