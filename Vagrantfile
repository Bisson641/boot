# -*- mode: ruby -*-
# vim: set ft=ruby :
home = ENV['HOME']
ENV["LC_ALL"] = "en_US.UTF-8"

MACHINES = {
    :Boot => {
        :box_name => "centos/7",
        :box_version => "1804.02",
        :ip_addr => '192.168.56.41',
        :path => 'boot.sh'
    }, 
}

Vagrant.configure("2") do |config|
    config.vm.box_version = "1804.02"
    MACHINES.each do |boxname, boxconfig|
        config.vm.define boxname do |box|
            box.vm.box = boxconfig[:box_name]
            box.vm.host_name = boxname.to_s  
            box.vm.network "private_network", ip: boxconfig[:ip_addr]  
            box.vm.provider :virtualbox do |vb|
                vb.customize ["modifyvm", :id, "--memory", "256"]
                needsController = false
            end#box.vm.provider 
            box.vm.provision "shell", path: boxconfig[:path]
        end#config.vm.define
    end#MACHINES
end #Vagrant.config
