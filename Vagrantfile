# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_IP       = "172.16.8.252"
NODE_IP_SUBNET  = "172.16.8"

KUBE_NUM_WORKERS = ENV['KUBE_NUM_WORKERS'] || 2
MASTER_CPU = 1
MASTER_MEM = 2048
WORKER_CPU = 1
WORKER_MEM = 1024

Vagrant.configure("2") do |config|
  config.vm.box = "geerlingguy/ubuntu2004"
  config.vm.box_version = "1.0.3"

  boxes = Array.new
  boxes.push({ :name => "master", :ip => MASTER_IP, :cpus => MASTER_CPU, :memory => MASTER_MEM })

  for i in 1..KUBE_NUM_WORKERS do 
    boxes.push({ :name => "node#{i}", :ip => "#{NODE_IP_SUBNET}.#{i}", :cpus => WORKER_CPU, :memory => WORKER_MEM })
  end

  boxes.each do |opts|
    config.vm.define opts[:name] do |box|
      box.vm.hostname = opts[:name]
      box.vm.network :private_network, ip: opts[:ip]
 
      box.vm.provider "virtualbox" do |vb|
        vb.cpus = opts[:cpus]
        vb.memory = opts[:memory]
      end
      box.vm.provision "shell", path:"./install-kubernetes-dependencies.sh", args: "#{KUBE_NUM_WORKERS}"
      if box.vm.hostname == "master" then 
        box.vm.provision "shell", path:"./configure-master-node.sh"
      else
        box.vm.provision "shell", path:"./configure-worker-nodes.sh"
      end

    end
  end
end
