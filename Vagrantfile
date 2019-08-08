# vim: set filetype=ruby:

require 'yaml'
require 'vagrant-hostmanager'
require 'vagrant-vbguest' unless defined? VagrantVbguest::Config
#require 'pp'

# Load configuration data from 'vagrant.yaml'
configuration_file = File.expand_path("vagrant.yaml", File.dirname(__FILE__))
configuration = YAML.load_file(configuration_file)
settings = configuration['vagrant']
puppetmaster = configuration['puppetmaster']
instances = configuration['instances']

# Create environment
Vagrant.configure("2") do |config|

  def check_dependency(plugin_name)
    unless Vagrant.has_plugin?(plugin_name)
      puts "Vagrant [" + plugin_name + "] is required but is not installed\n" +
           "please check if you have the plugin with the following command:\n" +
           "    $ vagrant plugin list\n" +
           "If needed install the plugin:\n" +
           "    $ vagrant plugin install " + plugin_name + "\n"
      abort "Missing [" + plugin_name + "] plugin\n\n"
    end
  end

  check_dependency 'vagrant-vbguest'
  check_dependency 'vagrant-hostmanager'

  # configure image
  config.vm.box              = settings['box']
  config.vm.box_check_update = settings['box_check_update']
  config.ssh.insert_key      = settings['ssh_insert_key']
  config.vbguest.auto_update = settings['auto_update']
  
  # configure network
  config.vm.network "private_network", type: "dhcp"

  # configure shared folder - for Development
  config.vm.synced_folder ".", "/vagrant", disabled: true

  if settings.has_key? 'hostmanager' and settings['hostmanager']
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = true
  end # end hostmanager

  unless puppetmaster.has_key? 'deactivated' or puppetmaster['deactivated']

    config.vm.define puppetmaster['hostname'] do |guest|

      if puppetmaster.has_key? 'box'
        guest.vm.box = puppetmaster['box']
      end # end box

      if puppetmaster.has_key? 'box_url'
        guest.vm.box_url = puppetmaster['box_url']
      end # end box_url

      if puppetmaster.has_key? 'private_ip'
        guest.vm.network 'private_network', ip: puppetmaster['private_ip']
      end # end private_ip

      guest.vm.provider 'virtualbox' do |vb|
        if puppetmaster.has_key? 'cpu_execution_cap'
          vb.customize ["modifyvm", :id, "--cpuexecutioncap", puppetmaster['cpu_execution_cap'].to_s]
        end

        vb.customize ["modifyvm", :id, "--nictype1", "virtio"]
        vb.customize ["modifyvm", :id, "--nictype2", "virtio"]

        if puppetmaster.has_key? 'memory'
          vb.memory = puppetmaster['memory']
        end

        if puppetmaster.has_key? 'cores'
          vb.cpus = puppetmaster['cores']
        end
      end # end vb

      if puppetmaster.has_key? 'mounts'
        puppetmaster['mounts'].each do |mount|
          guest.vm.synced_folder mount['host_path'], mount['guest_path'], owner: mount['owner'], group: mount['group']
        end
      end # end mounts

    end # config.vm.define puppetmaster

  end # if puppetmaster

  instances.each do |instance|

    next if instance.has_key? 'deactivated' and instance['deactivated']

    config.vm.define instance['hostname'] do |guest|

      if instance.has_key? 'box'
        guest.vm.box = instance['box']
      end
      if instance.has_key? 'box_url'
        guest.vm.box_url = instance['box_url']
      end

      if instance.has_key? 'private_ip'
        guest.vm.network 'private_network', ip: instance['private_ip']
      end

      guest.vm.provider 'virtualbox' do |vb|
        if instance.has_key? 'cpu_execution_cap'
          vb.customize ["modifyvm", :id, "--cpuexecutioncap", instance['cpu_execution_cap'].to_s]
        end

        vb.customize ["modifyvm", :id, "--nictype1", "virtio"]
        vb.customize ["modifyvm", :id, "--nictype2", "virtio"]

        if instance.has_key? 'memory'
          vb.memory = instance['memory']
        end

        if instance.has_key? 'cores'
          vb.cpus = instance['cores']
        end
      end

      if instance.has_key? 'mounts'
        instance['mounts'].each do |mount|
          guest.vm.synced_folder mount['host_path'], mount['guest_path'], owner: mount['owner'], group: mount['group']
        end
      end

    end # config.vm.define instance

  end # instances.each

end