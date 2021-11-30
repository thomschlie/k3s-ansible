# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_COUNT = 2
NODE_COUNT = 3
IMAGE = "ubuntu/bionic64"
VM_NETWORK = "k3s_cluster"

Vagrant.configure("2") do |config|

  (1..MASTER_COUNT).each do |i|
    config.vm.define "master#{i}" do |master|
      master.vm.box = IMAGE
      master.vm.hostname = "master#{i}"
      master.vm.network  :private_network, virtualbox__hostnet: VM_NETWORK, ip: "10.0.0.#{i+10}"
    end
  end

  (1..NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.box = IMAGE
      node.vm.hostname = "node#{i}"
      node.vm.network  :private_network, virtualbox__hostnet: VM_NETWORK, ip: "10.0.0.#{i+50}"
      if i == NODE_COUNT
        node.vm.provision "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "site.yml"
          ansible.groups = {
            "master" => [*1..MASTER_COUNT].map{ |i| "master#{i}"},
            "node" => [*1..NODE_COUNT].map{ |i| "node#{i}"},
            "k3s_cluster:children" => ["master", "node"]
          }
          ansible.extra_vars = {
            "k3s_version": "v1.22.3+k3s1",
            "master_ip": "\{\{hostvars[groups['master'][0]].ansible_enp0s8.ipv4.address\}\}",
            "systemd_dir": "/etc/systemd/system",
            "extra_agent_args": "--flannel-iface=enp0s8 --node-ip=\{\{ansible_enp0s8.ipv4.address\}\}",
            "extra_server_args": "--flannel-iface=enp0s8 --node-ip=\{\{ansible_enp0s8.ipv4.address\}\}"}
        end
      end
    end
  end

  config.vm.provider "virtualbox" do |vbox|
    vbox.linked_clone = true
  end

  config.vm.box_check_update = false

  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end

end