# Puppet learning VM

Learn / Spike (research) / Dev Puppet environment based on the official Puppet learning VM

## Generating the Learning VM vagrant box

<details>
  <summary>TODO</summary>
  Generate this image dynamically and create a Travis.CI Job to generate it.
</details>

Download `learning_puppet_vm-x.y.zip` and unzip it. (e.g. for `6.10`)

```bash
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