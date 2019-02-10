# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/centos-7.5"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end
  
  config.vm.define "server1" do |server1|
    server1.vm.hostname = "server1"
    server1.vm.network "private_network", ip: "192.168.0.10"
    server1.vm.provision "shell", inline: <<-SHELL
    if grep -q '192.168.0.1' /etc/hosts; then :;
      else echo '192.168.0.10 server1' >> /etc/hosts
           echo '192.168.0.11 server2' >> /etc/hosts
           echo '192.168.0.12 server3' >> /etc/hosts
    fi
    yum install httpd -y
    systemctl enable httpd
    systemctl start httpd
    firewall-cmd --zone=public --add-port=80/tcp --permanent
    firewall-cmd –-reload
    systemctl stop firewalld
    cp /vagrant/mod_jk.so /etc/httpd/modules/
    touch /etc/httpd/conf/workers.properties
    echo "worker.list=lb
    worker.lb.type=lb
    worker.lb.balance_workers=server2, server3
    worker.server2.host=192.168.0.11
    worker.server2.port=8008
    worker.server2.type=ajp13
    worker.server3.host=192.168.0.12
    worker.server3.port=8009
    worker.server3.type=ajp13"> /etc/httpd/conf/workers.properties
    echo "LoadModule jk_module modules/mod_jk.so
    JkWorkersFile conf/workers.properties
    JkShmFile /tmp/shm
    JkLogFile logs/mod_jk.log
    JkLogLevel info
    JkMount / lb" >> /etc/httpd/conf/httpd.conf
    systemctl restart httpd
    SHELL
  end
  
  config.vm.define "server2" do |server2|
    server2.vm.hostname = "server2"
    server2.vm.network "private_network", ip: "192.168.0.11"
    server2.vm.provision "shell", inline: <<-SHELL
    if grep -q '192.168.0.1' /etc/hosts; then :;
      else echo '192.168.0.10 server1' >> /etc/hosts
           echo '192.168.0.11 server2' >> /etc/hosts
           echo '192.168.0.12 server3' >> /etc/hosts
    fi
    yum install java-1.8.0-openjdk
    yum install tomcat tomcat-webapps tomcat-admin-webapps -y
    sed -i s/8009/8008/ /etc/tomcat/server.xml
    echo "<!doctype html>
    <html>
      <head>
        <title>SERVER2</title>
      </head>
      <body>
        <p>Tomcat SERVER2</p>
      </body>
    </html>"> /var/lib/tomcat/webapps/ROOT/index.jsp
    systemctl enable tomcat
    systemctl start tomcat  
    SHELL
  end
  
  config.vm.define "server3" do |server3|
    server3.vm.hostname = "server3"
    server3.vm.network "private_network", ip: "192.168.0.12"
    server3.vm.provision "shell", inline: <<-SHELL
    if grep -q '192.168.0.1' /etc/hosts; then :;
      else echo '192.168.0.10 server1' >> /etc/hosts
           echo '192.168.0.11 server2' >> /etc/hosts
           echo '192.168.0.12 server3' >> /etc/hosts
    fi
    yum install java-1.8.0-openjdk -y
    yum install tomcat tomcat-webapps tomcat-admin-webapps -y
    echo "<!doctype html>
    <html>
      <head>
        <title>SERVER3</title>
      </head>
      <body>
        <p>Tomcat SERVER3</p>
      </body>
    </html>"> /var/lib/tomcat/webapps/ROOT/index.jsp
    systemctl enable tomcat
    systemctl start tomcat
    SHELL
  end  
end
