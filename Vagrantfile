BOX_IMAGE = "ubuntu/trusty64"

Vagrant.configure("2") do |config|

  node_count = nil
  
  until (1..253).include?(node_count)
    print "Enter the number of VMs (1-253): "
    node_count = STDIN.gets.chomp.to_i
    
    if !node_count.is_a?(Numeric) || node_count < 1 || node_count > 253
      puts "Invalid input. Please enter a number between 1 and 253."
      node_count = nil
    end
  end


  (1..node_count).each do |i|
    config.vm.define "VM#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "vm#{i}"
      subconfig.vm.network :private_network, ip: "192.168.5.#{i + 10}"
    end
  end
end
  
