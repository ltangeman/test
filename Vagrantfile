# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_DEFAULT_PROVIDER'] = 'libvirt'
ENV[`whoami`] = 'USER'
require 'yaml'
dir_var = YAML.load_file './roles/common/defaults/main.yaml'

required_plugins = %w(vagrant-libvirt fog sahara)

plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end


Vagrant.configure(2) do |config|
  config.vm.synced_folder '.', '/vagrant', :disabled => true
  #config.vm.synced_folder 'sync/', '/vagrant', :create => true
  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
    # If use ssh tunnel to connect to Libvirt.
    libvirt.connect_via_ssh = false
    libvirt.username = "bleh"
  end

  config.vm.define dir_var['director_hostname'] do |director|
    director.vm.box = "centos/7"

    director.vm.hostname = "dir1"
    director.vm.provider :libvirt do |main|
      main.driver = "kvm"
      main.memory = "4096"
      main.cpus = "2"
      main.nested = true
    end
    director.vm.network :private_network,
      :network_name => "laron-prov",
      :ip => "10.10.10.11,
      :libvirt__netmask => "255.255.255.0"
    end
  config.vm.provision "ansible" do |ansible|
    ansible.sudo = true
    ansible.playbook = "pre-prep.yaml"
    #ansible.verbose = "vv"
    #ansible.host_key_checking = false
  end
end
