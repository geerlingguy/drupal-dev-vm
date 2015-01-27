# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

# Use config.yml for basic VM configuration.
require 'yaml'
if !File.exist?('./config.yml')
  raise 'Configuration file not found! Please copy example.config.yml to config.yml and try again.'
end
vconfig = YAML::load_file("./config.yml")

# Use rbconfig to determine if we're on a windows host or not.
require 'rbconfig'
is_windows = (RbConfig::CONFIG['host_os'] =~ /mswin|mingw|cygwin/)

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.hostname = vconfig['vagrant_hostname']
  config.vm.network :private_network, ip: vconfig['vagrant_ip']
  config.ssh.insert_key = false

  config.vm.box = "puphpet/ubuntu1404-x64"

  for synced_folder in vconfig['vagrant_synced_folders'];
    config.vm.synced_folder synced_folder['local_path'], synced_folder['destination'],
      type: synced_folder['type'],
      # nfs_export: false, # Uncomment this line once exports have been configured
      id: synced_folder['id']
  end

  if is_windows
    # Provisioning configuration for shell script (for Windows).
    config.vm.provision "shell" do |sh|
      sh.path = "provisioning/JJG-Ansible-Windows/windows.sh"
      sh.args = "provisioning/playbook.yml"
    end
  else
    # Provisioning configuration for Ansible (for Mac/Linux hosts).
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/playbook.yml"
      ansible.sudo = true
    end
  end

  # VirtualBox.
  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--name", vconfig['vagrant_hostname']]
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--memory", vconfig['vagrant_memory']]
    v.customize ["modifyvm", :id, "--cpus", vconfig['vagrant_cpus']]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
  end

  # Set the name of the VM. See: http://stackoverflow.com/a/17864388/100134
  config.vm.define :ilrdev do |drupaldev_config|
  end
end
