# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  N = 3
  (1..N).each do |server_id|
    config.vm.define "node#{server_id}" do |server|
      server.vm.box = "ubuntu/bionic64"
      server.vm.hostname = "node#{server_id}"
      server.vm.network "private_network", ip: "172.28.128.25#{server_id}"
      if server_id == N
        server.vm.provision :ansible do |ansible|
          ansible.limit = "all"
          ansible.playbook = "playbooks/deploy.yml"
          ansible.compatibility_mode = "2.0"
        end
      end
    end
  end
  config.vm.provider "virtualbox" do |v|
    v.memory = 4192
    v.cpus = 2
  end
end
