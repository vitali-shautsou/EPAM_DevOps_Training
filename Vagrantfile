# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end
  
  config.vm.define "server1" do |server1|
    server1.vm.hostname = "server1"
    server1.vm.network "private_network", ip: "192.168.0.10"
    server1.vm.provision "shell", inline: <<-SHELL
    if grep -q '192.168.0.11 server2' /etc/hosts; then :;
      else echo '192.168.0.11 server2' >> /etc/hosts;
    fi
    SHELL
  end
  
  config.vm.define "server2" do |server2|
    server2.vm.hostname = "server2"
    server2.vm.network "private_network", ip: "192.168.0.11"
    server2.vm.provision "shell", inline: <<-SHELL
      apt-get install git -y 
      if [[ ! -d "/home/vagrant/EPAM_DevOps_Training/" ]]; then 
        git clone git://github.com/vitali-shautsou/EPAM_DevOps_Training
      fi
      cd EPAM_DevOps_Training/
      git checkout origin/task2
      cat test.txt
      if grep -q '192.168.0.10 server1' /etc/hosts; then :;
        else echo '192.168.0.10 server1' >> /etc/hosts;
      fi
    SHELL
  end
end
