VAGRANTFILE_API_VERSION = "2"

mem = ENV['memory']

cluster = {
  "docker1" => { :ip => "192.0.3.11", :cpus => 1, :mem => mem },
  "docker2"  => { :ip => "192.0.3.12", :cpus => 1, :mem => mem },
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.ssh.insert_key = false
    config.vm.box = "bento/centos-7.1"
    config.vm.box_check_update = false
    ssh_pub_key = File.readlines("key.pub").first.strip
    #config.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys"
    config.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys"
    

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|
      cfg.vm.provider :virtualbox do |vb, override|
        override.vm.hostname = hostname
        override.vm.network :private_network, ip: "#{info[:ip]}"
        vb.name = hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on" ]
      end    
      cfg.vm.provision :hosts do |provisioner|
        cluster.each do |(host, i)|
          provisioner.add_host i[:ip], [host]
        end        
      end 
    end
  end 
end 
