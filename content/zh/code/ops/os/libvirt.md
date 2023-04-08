
---
title: "libvirt"
---

## install

- mac
```shell
brew install qemu gcc libvirt
brew install virt-manager

# macOS doesn't support QEMU security features
echo 'security_driver = "none"' >> /opt/homebrew/etc/libvirt/qemu.conf
echo "dynamic_ownership = 0" >> /opt/homebrew/etc/libvirt/qemu.conf
echo "remember_owner = 0" >> /opt/homebrew/etc/libvirt/qemu.conf

brew services start libvirt
```

## vagrant

```ruby
# https://developer.fedoraproject.org/tools/vagrant/vagrant-libvirt.html
# https://vagrant-libvirt.github.io/vagrant-libvirt/installation.html

# vagrant plugin install vagrant-libvirt
Vagrant.configure("2") do |config|
  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "qemu"
  end
end
# export VAGRANT_DEFAULT_PROVIDER=libvirt
# vagrant up --provider=libvirt
```

## create

### from iso

```shell
mkdir ~/vms && cd ~/vms

qemu-img create -f qcow2 debian.qcow2 50g
virsh define debian.xml
virsh start debian
virsh list
```

```xml
<domain type='hvf' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>
    <name>debian</name>
    <uuid>09EF3223-7CCD-43BD-9D1A-C36A379A5938</uuid>
    <memory unit='GiB'>2</memory>
    <cpu mode='custom' match="exact">
       <model>host</model>
    </cpu>
    <vcpu>2</vcpu>
    <features>
        <gic version='2'/>
    </features>
    <os firmware='efi'>
        <type arch='aarch64' machine='virt'>hvm</type>
    </os>
    <clock offset='localtime'/>
    <devices>
        <emulator>/opt/homebrew/bin/qemu-system-aarch64</emulator>
        <controller type='usb' model='qemu-xhci'/>
        <disk type='file' device='disk'>
            <driver name='qemu' type='qcow2'/>
            <source file='/Users/tuke/vms/debian9-amd64.qcow2'/>
            <target dev='vda' bus='virtio'/>
            <boot order='1'/>
        </disk>
        <disk type='file' device='disk'>
            <driver name='qemu' type='raw'/>
            <source file='/Users/tuke/vms/debian9-amd64.iso'/>
            <target dev='vdb' bus='virtio'/>
            <boot order='2'/>
        </disk>
        <console type='pty'>
            <target type='serial'/>
        </console>
        <input type='tablet' bus='usb'/>
        <input type='keyboard' bus='usb'/>
        <graphics type='vnc' port='5900' listen='127.0.0.1'/>
        <video>
            <model type='virtio' vram='32768'/>
        </video>
    </devices>
    <seclabel type='none'/>
    <qemu:commandline>
        <qemu:arg value='-machine'/>
        <qemu:arg value='highmem=off'/>
        <qemu:arg value='-netdev'/>
        <qemu:arg value='user,id=n1,hostfwd=tcp::2222-:22'/>
        <qemu:arg value='-device'/>
        <qemu:arg value='virtio-net-pci,netdev=n1,bus=pcie.0,addr=0x19'/>
    </qemu:commandline>
</domain>
```

### from qcow2/raw

```shell
# https://cdimage.debian.org/images/cloud/stretch/daily/
# yum install qemu-kvm qemu-kvm-tools virt-manager libvirt virt-install –y

virt-install --name debian --ram 2048 --vcpus=2 --disk path=debian.qcow2 --network=bridge:en0 --force --import --autostart
virsh console --domain debian --force

# KVM format convert
qemu-img convert -p -t directsync -O qcow2 test.raw test.qcow2
qemu-img convert -p -t directsync -O raw test.qcows test.raw
```
