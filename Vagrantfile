# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  ANSIBLE_PLAYBOOK = "full-setup.yml"
  INVENTORY_FILE = "inventory.yml"

  config.vm.define "monitor" do |monitor|
    monitor.vm.hostname = "monitor"
    monitor.vm.network "private_network", ip: "192.168.56.10"

    monitor.vm.provision "ansible" do |ansible|
      ansible.playbook = ANSIBLE_PLAYBOOK
      ansible.inventory_path = INVENTORY_FILE
      ansible.limit = "monitor"
      ansible.verbose = "v"
    end
  end
  
  config.vm.define "web-server" do |web|
    web.vm.hostname = "web-server"
    web.vm.network "private_network", ip: "192.168.56.11"


    web.vm.provision "ansible" do |ansible|
      ansible.playbook = ANSIBLE_PLAYBOOK
      ansible.inventory_path = INVENTORY_FILE
      ansible.limit = "web-server"
      ansible.verbose = "v"
    end
  end


  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  config.ssh.insert_key = true
end
