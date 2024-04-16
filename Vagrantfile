Vagrant.configure("2") do |config|

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  config.vm.box = "hashicorp/bionic64"

  # Prompt for the number of VMs
  vm_count = nil

    until (1..253).include?(vm_count)
      print "Enter the number of VMs (1-253): "
      vm_count = STDIN.gets.chomp.to_i

      if !vm_count.is_a?(Numeric) || vm_count < 1 || vm_count > 253
        puts "Invalid input. Please enter a number between 1 and 253."
        vm_count = nil
      end
    end

  # Launch webserver VM
  config.vm.define "webserver" do |webserver|
    webserver.vm.hostname = "webserver"
    webserver.vm.network "forwarded_port", guest: 80, host: 8080
    webserver.vm.network "private_network", ip: "192.168.5.10"
    webserver.vm.provider "virtualbox" do |vb|
      vb.name = "webserver"
    end
    # Provision webserver
    webserver.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y nginx
    sudo mkdir -p /var/www/html/
    echo "Welcome to the webserver!" | sudo tee /var/www/html/index.html

    # Configure load balancing
    echo "http {" | sudo tee /etc/nginx/nginx.conf
    echo "    upstream backend {" | sudo tee -a /etc/nginx/nginx.conf
    echo "        server 192.168.5.11;" | sudo tee -a /etc/nginx/nginx.conf
    echo "        server 192.168.5.12;" | sudo tee -a /etc/nginx/nginx.conf
    echo "        server 192.168.5.13;" | sudo tee -a /etc/nginx/nginx.conf
    echo "    }" | sudo tee -a /etc/nginx/nginx.conf
    echo "    server {" | sudo tee -a /etc/nginx/nginx.conf
    echo "        listen 8080;" | sudo tee -a /etc/nginx/nginx.conf
    echo "        server_name example.com;" | sudo tee -a /etc/nginx/nginx.conf
    echo "        location / {" | sudo tee -a /etc/nginx/nginx.conf
    echo "            proxy_pass http://192.168.5.11;" | sudo tee -a /etc/nginx/nginx.conf
    echo "        }" | sudo tee -a /etc/nginx/nginx.conf
    echo "    }" | sudo tee -a /etc/nginx/nginx.conf
    echo "}" | sudo tee -a /etc/nginx/nginx.conf
    echo "events {}" | sudo tee -a /etc/nginx/nginx.conf 
    # Restart NGINX
    sudo service nginx restart

    SHELL
  end

  if vm_count > 1
    # Launch Tomcat VMs
    (1..(vm_count - 1)).each do |i|
     config.vm.define "tomcat#{i}" do |tomcat|
       tomcat.vm.hostname = "tomcat#{i}"
       tomcat.vm.network "private_network", ip: "192.168.5.#{i + 10}"
       tomcat.vm.provider "virtualbox" do |vb|
         vb.name = "tomcat#{i}"
       end
       # Provision Tomcat VMs
       tomcat.vm.provision "shell", inline: <<-SHELL
         echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
         echo "export JRE_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre" >> ~/.bashrc
         source ~/.bashrc
         sudo apt-get update
         sudo apt-get install -y openjdk-8-jdk
         wget --no-check-certificate https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.107/bin/apache-tomcat-7.0.107.tar.gz
         tar -xf apache-tomcat-7.0.107.tar.gz
         sudo mkdir -p /opt/tomcat7
         sudo mv apache-tomcat-7.0.107/* /opt/tomcat7/
         sudo chown -R vagrant:vagrant /opt/tomcat7
         sudo sed -i 's/Connector port="8080"/Connector port="8080" address="0.0.0.0"/' /opt/tomcat7/conf/server.xml
         echo "Welcome to Tomcat on #{tomcat.vm.hostname}!" > /opt/tomcat7/webapps/ROOT/index.jsp
         sudo /opt/tomcat7/bin/startup.sh
       SHELL
     end
    end
  end
end
