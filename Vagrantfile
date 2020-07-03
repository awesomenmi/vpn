# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {

  :server => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.0.1', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dir-net"},
                   
                ]
  },

  :client => {
        :box_name => "centos/7",
        :net => [
                    {ip: '192.168.0.2', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dir-net"},
                ]
  },

  :ovpnserver => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.20.1', adapter: 5, netmask: "255.255.255.0", virtualbox__intnet: "dir-net"},
                   
                ]
  },

}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|
      
    config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s

        config.vm.provider "virtualbox" do |v|
          v.memory = 256
        end

        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
	 # box.vm.network "public_network"
        end

        box.vm.provision "shell", inline: <<-SHELL
          mkdir -p ~root/.ssh
                cp ~vagrant/.ssh/auth* ~root/.ssh
        SHELL
        
    end
  end

  config.vm.provision "ansible" do |ansible|
  	ansible.playbook = "provision.yml"
        #ansible.verbose = "vv"
  end

end
