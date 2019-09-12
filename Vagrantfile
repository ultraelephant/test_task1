# -*- mode: ruby -*-
# vi: set ft=ruby :

IMAGE = "ubuntu/xenial64"
VM_VRRP_COUNT = 3
VM_COUNT = 6


Vagrant.configure("2") do |config|
  (1..VM_VRRP_COUNT).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.box = IMAGE
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.network :private_network, ip: "192.168.0.#{i + 10}"
      diskname = "./secdisk_node#{i}.vdi"
      if i == 1
        subconfig.vm.provision :ansible do |ansible|
          ansible.playbook = "vrrp.yml"
          ansible.extra_vars = {
            role:"MASTER",
            priority:"100"
          }
	end
      else
	subconfig.vm.provision :ansible do |ansible|
            ansible.playbook = "vrrp.yml"
            ansible.extra_vars = {
              role:"BACKUP",
              priority:"#{100 + i}"
            }
	end
      end
    end
  end
  (VM_VRRP_COUNT+1..VM_COUNT).each do |y|
    config.vm.define "node#{y}" do |subconfig|
      subconfig.vm.box = IMAGE
      subconfig.vm.hostname = "node#{y}"
      subconfig.vm.network :private_network, ip: "192.168.0.#{y + 10}"
    end
    if y == VM_COUNT
      config.vm.provision :ansible do |ansible|
        ansible.playbook = "minio.yml"
      end
    end
  end
end
