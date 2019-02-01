# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end
  
  config.vm.define "server2" do |server2|
    server2.vm.hostname = "server2"
    server2.vm.network "private_network", ip: "192.168.0.11"
	server2.vm.provision "shell", inline: <<-SHELL
	if grep -q '192.168.0.10 server1' /etc/hosts; then :;
	  else echo '192.168.0.10 server1' >> /etc/hosts;
    fi
	ssh-keygen -t rsa -f /home/vagrant/.ssh/id_rsa -q -P ""
	# Copy id_rsa.pub to shared directory;
	# this file server1 will use after booting:
	cp /home/vagrant/.ssh/id_rsa.pub /vagrant/id_rsa.pub_server2
	chown vagrant:vagrant /home/vagrant/.ssh/id_rsa
	# Create file id_rsa.pub_server1 in shared directory; 
	# server1 will copy it id_rsa.pub in this file (see line 43):
	touch /vagrant/id_rsa.pub_server1
	# Create crontab task to synchronize authorized_keys file with id_rsa.pub_server1 from server1:
	echo '* * * * * cat /vagrant/id_rsa.pub_server1 > /home/vagrant/.ssh/authorized_keys' > mycron
	crontab -u vagrant mycron
	SHELL
  end
  
  config.vm.define "server1" do |server1|
    server1.vm.hostname = "server1"
    server1.vm.network "private_network", ip: "192.168.0.10"
	server1.vm.provision "shell", inline: <<-SHELL
	if grep -q '192.168.0.11 server2' /etc/hosts; then :;
	  else echo '192.168.0.11 server2' >> /etc/hosts;
	fi
	# Update authorized_keys file:
	cat /vagrant/id_rsa.pub_server2 > /home/vagrant/.ssh/authorized_keys
	ssh-keygen -t rsa -f /home/vagrant/.ssh/id_rsa -q -P ""
	chown vagrant:vagrant /home/vagrant/.ssh/id_rsa
	# Copy id_rsa.pub to shared directory:
	cp /home/vagrant/.ssh/id_rsa.pub /vagrant/id_rsa.pub_server1
	SHELL
  end
end
