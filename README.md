# Puppet learning VM

Learn / Spike (research) / Dev Puppet environment based on the official Puppet learning VM. 

<img src='https://upload.wikimedia.org/wikipedia/en/0/09/Puppet%27s_company_logo.png' width='200px'>

> Puppet is an open-core software configuration management tool. It runs on many Unix-like systems as well as on Microsoft Windows, and includes its own declarative language to describe system configuration. It is written in C++, Clojure and Ruby, with its free-software version released under the Apache License 2.0 - [wikipedia](https://en.wikipedia.org/wiki/Puppet_(company)#Puppet)

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

## Using the VM

Setup your environment (how many VMs you want)
```bash
cp vagrant.yaml.example vagrant.yaml
```

To spin up a puppet master:
```bash
vagrant up learning
```

To `ssh` into the box:
```
vagrant ssh learning
```

Once `ssh` into the `learning` VM you can get root by:
```
[vagrant@learning ~]$ sudo su -
root@learning: # ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group (...)
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:b9:88:bd brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic eth0
       valid_lft 85677sec preferred_lft 85677sec
    inet6 fe80::752c:860f:3985:c1a2/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:68:fe:a9 brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.8/24 brd 172.28.128.255 scope global noprefixroute dynamic eth1
       valid_lft 1048sec preferred_lft 1048sec
    inet6 fe80::a00:27ff:fe68:fea9/64 scope link 
       valid_lft forever preferred_lft forever
(...)
```

`172.28.128.8` from `eth1` is the IP that you care. Everytime you reboot the VM, you may get a different IP.

To use the Quest Guide UI, go to: http://172.28.128.8/ (replace with your own IP). You should be able to see:
```
Quest Guide for the Puppet Learning VM
```
To use the Puppet Console UI, go to https://172.28.128.8/ (replace with your own IP) and accept the self-signed certificate. You should be able to see:
```
Overview
```

Login using the default credentials:
* username: `admin`
* password: `puppetlabs`

### Alternative - using `hostmanager`

For this, you need to enable `hostmanager: true` in `vagrant.yaml`

To use the Quest Guide UI, go to: http://puppetmaster/. You should be able to see:
```
Quest Guide for the Puppet Learning VM
```
To use the Puppet Console UI, go to https://puppetmaster/ and accept the self-signed certificate. You should be able to see:
```
Overview
```

This is possible because on `linux` (if your machine is `linux`) or `macos`, for your convenience, `vagrant` will use the `hostmanager` plugin to add the node to your `/etc/hosts` file.

By default, the VM will be created in a NAT network, so `hostmanager` will use the internal routing to get to your node. The node is setup to use `dhcp` (by default). You can change this by modifying in `vagrant.yaml` the `private_ip` field.

The image is based on `CentOS Linux 7 (Core)` and the default clients are abstracted as `docker` containers.

## Official Learning VMs

<details>
<p>

Information was extracted using:
```
for i in $(ls -1 learning_puppet_vm*.zip); do unzip -ql $i; done | grep ova | awk '{print $4 " | " $2 " " $3 " | " $1}' | sort
```
</p>
</details>

VM | time | size (KB) 
-|-|-
puppet-2017.2.2-learning-6.1.ova | 2017-08-08 16:43 | 3455068160
puppet-2017.3.2-learning-6.3.ova | 2017-12-04 12:16 | 4316023808
puppet-2017.3.2-learning-6.4.ova | 2017-12-06 18:04 | 3906296320
puppet-2017.3.5-learning-6.5.ova | 2018-03-15 02:31 | 3291561472
puppet-2019.0.1-learning-6.6.ova | 2019-01-17 20:10 | 3035691008
puppet-2019.0.1-learning-6.6.ova | 2019-01-17 20:10 | 3035691008
puppet-2019.0.1-learning-6.7.ova | 2019-01-23 01:29 | 3055212544
puppet-2019.0.2-learning-6.8.ova | 2019-03-15 14:24 | 3179167232
puppet-2019.0.2-learning-6.9.ova | 2019-03-27 20:59 | 3183727104
puppet-2019.0.2-learning-6.10.ova | 2019-04-30 17:41 | 3375168000

## Generating the Learning VM vagrant box

<details>
  <summary>TODO</summary>
  Generate this image dynamically and create a Travis.CI Job to generate it.
</details>

Download latest `learning_puppet_vm.zip` and unzip it.

```
wget https://pe-education-vms.s3.amazonaws.com/learning/learning_puppet_vm.zip
unzip learning_puppet_vm.zip
```
or for a specific version (e.g. `6.10`):
```bash
wget https://pe-education-vms.s3.amazonaws.com/learning/learning_puppet_vm-6.10.zip
unzip learning_puppet_vm-6.10.zip
```

After unziping it, there is an `.ova` image. Import it into VirtualBox:
```bash
VBoxManage import puppet-2019.0.2-learning-6.10.ova --vsys 0 --eula accept --vsys 0 --vmname "puppet-learning-vm-2019.0" --settingsfile "puppet-learning-vm"
```

List all your VMs and retrieve the `puppet-learning-vm`:
```bash
$ # list all your VMs
$ VBoxManage list --sorted vms
"puppet-learning-vm-2019.0" {28f73b71-7b70-4838-800b-d67400593594}
$ # list only the puppet-learning-vm-2019.0 VM
$ VBoxManage list --sorted vms | grep 'puppet-learning-vm-2019.0'
"puppet-learning-vm-2019.0" {28f73b71-7b70-4838-800b-d67400593594}
$ # list only the VM ID
$ VBoxManage list --sorted vms | grep 'puppet-learning-vm-2019.0' | awk '{print $2}' | cut -d '{' -f2 | cut -d '}' -f1
28f73b71-7b70-4838-800b-d67400593594
```

Package the VM:
```bash
vagrant package --base '28f73b71-7b70-4838-800b-d67400593594' --output puppet-learning-vm.box
```

_Optimization_: If you want to do it in oneshot, to get the VM UUID and package it as a `.box` (vagrant image) file:
```bash
vagrant package --base $(VBoxManage list --sorted vms | grep 'puppet-learning-vm-2019.0' | awk '{print $2}' | cut -d '{' -f2 | cut -d '}' -f1) --output puppet-learning-vm.box
```

Publishing the `.box`:
```bash
vagrant cloud publish --box-version 6.10-0 azbarcea/puppet-learning-vm-2019.0 --release --description "Puppet learning VM containing the Quest to learn (https://puppet.com/blog/quest-to-learn-puppet-new-learning-vm)" --short-description "Puppet learning VM for PE 2019.0" --version-description "Based on the official puppet-2019.0.2-learning-6.10.ova from learning_puppet_vm-6.10.zip" 6.10-0 virtualbox puppet-learning-vm-2019.0.box
```

The version `6.10-0` was used because `6.10` comes from the official `puppet-2019.0.2-learning-6.10.ova` (within `learning_puppet_vm-6.10.zip`) and `0` is the current `.box` packaging. 

# Known Issues

* You need to enable virtualization in your BIOS for Virtualbox to work
* On Windows, you need to disable Hyper-V from Windows Features for Virtualbox to work
* Encountered issues when `Guest GuestAdditions` was upgraded. This depends on your host Virtualbox and how much is up-to-date.
```
GuestAdditions versions on your host (6.0.10) and guest (6.0.6) do not match
```