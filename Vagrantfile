#
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  #start bash as a non-login shell (fix 'stdin: is not a tty' error)
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"  
  config.ssh.insert_key = false
  config.ssh.forward_agent = false
  config.ssh.pty = false

  #load balancer
  config.vm.define :lb do |lb_config|
      lb_config.vm.box = "ubuntu/xenial64"
      lb_config.vm.hostname = "lb"
      lb_config.vm.network :private_network, ip: "10.0.15.11"
      lb_config.vm.network "forwarded_port", guest: 80, host: 80
      lb_config.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize ["modifyvm", :id, "--memory", 512]
		    vb.customize ["modifyvm", :id, "--name", "lb"]
      end
      lb_config.vm.provision "ansible_local" do |ansible|
           ansible.limit = "all"
           ansible.inventory_path ="ansible/hosts"
           ansible.playbook = "ansible/playbooks/lb_playbook.yml"
      end
  end

  #db
  config.vm.define :db do |db_config|
      db_config.vm.box = "ubuntu/xenial64"
      db_config.vm.hostname = "db"
      db_config.vm.network :private_network, ip: "10.0.15.12"
      db_config.vm.network "forwarded_port", guest: 3306, host: 3306
      db_config.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize ["modifyvm", :id, "--memory", 512]
		    vb.customize ["modifyvm", :id, "--name", "db"]
      end
      db_config.vm.provision "ansible_local" do |ansible|
           ansible.limit = "all"
           ansible.inventory_path ="ansible/hosts"
           ansible.playbook = "ansible/playbooks/db_playbook.yml"
      end
  end

  #redis
  config.vm.define :redis do |redis_config|
      redis_config.vm.box = "ubuntu/xenial64"
      redis_config.vm.hostname = "redis"
      redis_config.vm.network :private_network, ip: "10.0.15.13"
      redis_config.vm.network "forwarded_port", guest: 6379, host: 6379
      redis_config.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize ["modifyvm", :id, "--memory", 512]
		    vb.customize ["modifyvm", :id, "--name", "redis"]
      end
      redis_config.vm.provision "ansible_local" do |ansible|
           ansible.limit = "all"
           ansible.inventory_path ="ansible/hosts"
           ansible.playbook = "ansible/playbooks/redis_playbook.yml"
      end
  end
  
  
  #web servers
  N=3 #web servers number
  (1..N).each do |i|
    config.vm.define "flask#{i}" do |node|
        node.vm.box = "ubuntu/xenial64"
        node.vm.hostname = "flask#{i}"
        node.vm.network :private_network, ip: "10.0.15.2#{i}"
        #node.vm.network "forwarded_port", guest: 5000, host: 5000
        node.vm.provider "virtualbox" do |vb|
          vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
          vb.customize ["modifyvm", :id, "--memory", 512]
		      vb.customize ["modifyvm", :id, "--name", "flask#{i}"]
        end
       if i == N
         node.vm.provision "ansible_local" do |ansible|
           ansible.limit = "all"
           ansible.inventory_path ="ansible/hosts"
           ansible.playbook = "ansible/playbooks/flask_playbook.yml"
         end
       end

    end
  end

end
