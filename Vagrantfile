# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.8.1" # Need a recent Vagrant to work well with Windows 10
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Virtual hosts used for Devoxx presentation
  hosts = [
    { :name => "app1", :address => "10.10.3.20" }, # application server #1
    { :name => "app2", :address => "10.10.3.21", :os=>"windows" },  # application server #2
  ]

  # Host setup template
  hosts.each do |params|
    config.vm.define "#{ params[:name] }" do |host|

      # host.vm.hostname = "#{ params[:name] }-ansible-hawtapp"
      memory = 512
      if params[:os]=="windows"

        host.vm.box = "modernIE/w10-edge"
        host.vm.guest = :windows
        host.vm.boot_timeout = 200
        host.ssh.username = "IEUser"
        host.ssh.password = "Passw0rd!"
        host.vm.communicator = "winrm"

        # Diable firewall so we can access the box on the assigned private network.
        host.vm.provision "shell", inline: 'netsh "advfirewall" "set" "allprofiles" "state" "off"'
        memory = 768 # Windows needs a little more memory than linux.
      else

        host.vm.box = "jimmidyson/centos-7.1"
        host.vm.box_version = "= 1.2.6"

        #host.vm.box = "Centos-6.5-minimal-x86_64-20140116"
        #host.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.3/centos65-x86_64-20140116.box"
        #host.vm.provision "shell", inline: "sudo yum -y install python-httplib2" # Required to use 'uri' Ansible module

      end

      host.vm.network :private_network, ip: "#{ params[:address] }"
      host.vm.provider "virtualbox" do |vb|
        vb.customize [
           "modifyvm", :id,
           "--name", "#{ params[:name] }-ansible-hawtapp",
           "--memory", memory,
           "--cpus", 1,
        ]
      end

    end
  end

end
