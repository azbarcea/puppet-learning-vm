# Puppet learning VM

Learn / Spike (research) / Dev Puppet environment based on the official Puppet learning VM

## Using the VM

Setup your environment (how many VMs you want)
```bash
cp vagrant.yaml.example vagrant.yaml
```

To spin up a puppet master:
```bash
vagrant up puppetmaster
```

To `ssh` into the box:
```
vagrant ssh puppetmaster
```

To use the UI, go to: https://puppetmaster/. You should be able to see:
```
Quest Guide for the Puppet Learning VM
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

Get the VM UUID and package it
```bash
vagrant package --base $(VBoxManage list --sorted vms | grep 'puppet-learning-vm-2019.0' | awk '{print $2}' | cut -d '{' -f2 | cut -d '}' -f1) --output puppet-learning-vm.box
```