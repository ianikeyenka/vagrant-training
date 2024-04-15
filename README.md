# Task 4
1. Create a Vagrantfile, in which you can specify the number of VMs to launch and configure, and the host part of each VM’s IP address changes based on the number of the VM (the network part of the IP is common, the host part is variable). The hostnames of the VMs must change the same way, and the information about all VMs must be added to /etc/hosts in each VM after the VMs are launched and provisioning is finished (see Task 3). 
2. The total number of VMs is input from keyboard after the ‘vagrant up’ command is run and can take a value between 1 and 253 with input validation 
3. When the number of VMs is specified, one of the VMs must have a webserver installed (Apache or NGINX), the other VMs must have Tomcat installed and contain an index.html inside /usr/share/tomcat/webapps/ with a welcome text and the name of the VM on which the page is located. The webserver must be configured as a load balancer (default load balancing algorithm). 
!!!! Note: if the number of VMs = 1, only the VM with webserver must be launched. In all other cases 1 webserver VM is launched, and the other VMs with Tomcat. 

