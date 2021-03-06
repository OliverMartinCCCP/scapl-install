# -*- mode: ruby -*-
# vi: set ft=ruby :

# IMPORTANT :
#  Don't forget to run the following commands to get the provider plugin installed !
#  $ vagrant plugin install vagrant-vmware-workstation
#  $ vagrant plugin license vagrant-vmware-workstation /path/to/license.lic
#  If you want to get the VMs installed in a specific directory, type:
#  Not working: $ export VAGRANT_VMWARE_CLONE_DIRECTORY=/path/to/your/vm/library
#  $ mkdir .vagrant && cd .vagrant
#  $ sudo ln -s /path/to/vm/library machines && cd ..

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

servers = ['frontend', 'search', 'automation', 'backbone', 'backend']

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "phusion/ubuntu-14.04-amd64"
#  config.vm.box_url = "https://atlas.hashicorp.com/boxcutter/boxes/ubuntu1404/versions/2.0.10/providers/vmware_desktop.box"
  config.vm.box_url = "https://oss-binaries.phusionpassenger.com/vagrant/boxes/latest/ubuntu-14.04-amd64-vmwarefusion.box"

  servers.each do |hostname|
    config.vm.define "scapl-#{hostname}" do |server|
      # networking
      case hostname
      when "backbone"
        server.vm.network :private_network, ip: "192.168.1.10"
      when "frontend"
        server.vm.network :private_network, ip: "192.168.1.20"
      when "search"
        server.vm.network :private_network, ip: "192.168.1.30"
      when "automation"
        server.vm.network :private_network, ip: "192.168.1.40"
      when "backend"
        server.vm.network :private_network, ip: "192.168.1.50"
      end

      # customize VM
      server.vm.hostname = "scapl-#{hostname}"
      config.vm.provider :vmware_workstation do |vmware|
        vmware.gui = true
        vmware.vmx["name"] = "scapl-#{hostname}"
        vmware.vmx["displayName"] = "scapl-#{hostname}"
        case hostname
        when "backbone" # give a bit more capability to backbone (supporting RabbitMQ and the FTP server)
          vmware.vmx["memsize"] = 1024
          vmware.vmx["numvcpus"] = 2
        when "backend"
          vmware.vmx["memsize"] = 2048 # give a bit more capability to backend (running Cassandra)
          vmware.vmx["numvcpus"] = 2
        else
          vmware.vmx["memsize"] = 512
          vmware.vmx["numvcpus"] = 1
        end
        vmware.vmx["usb.present"] = false # disable USB controller
        vmware.vmx["usb.vbluetooth.startConnected"] = false # disable BlueTooth
      end

      # baseline provisioning
      config.vm.provision "ansible" do |ansible|
        ansible.playbook = "provisioning/scapl-#{hostname}.yml"
        ansible.host_key_checking = false
        ansible.verbose = "v"
      end
    end
  end
end
