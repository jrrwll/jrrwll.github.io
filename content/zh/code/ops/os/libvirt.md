
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
