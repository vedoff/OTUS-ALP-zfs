# -*- mode: ruby -*-
# vim: set ft=ruby :
home = ENV['HOME']
ENV["LC_ALL"] = "en_US.UTF-8"

MACHINES = {
  :"zfs-01" => {
        :box_name => "vedoff/centos-7-5",
        :box_version => "1.0",
#        :ip_addr => '192.168.11.101',
        :net => [],
    :"disks" => {
        :sata1 => {
            :dfile => home + '/VirtualBoxHDD/VHDD/sata1.vdi',
            :size => 1024,
            :port => 1
        },
        :sata2 => {
            :dfile => home + '/VirtualBoxHDD/VHDD/sata2.vdi',
            :size => 1024, # Megabytes
            :port => 2
        },
        :sata3 => {
            :dfile => home + '/VirtualBoxHDD/VHDD/sata3.vdi',
            :size => 1024, # Megabytes
            :port => 3
        },
        :sata4 => {
            :dfile => home + '/VirtualBoxHDD/VHDD/sata4.vdi',
            :size => 1024,
            :port => 4
        }
    }
  },
}

Vagrant.configure("2") do |config|

    config.vm.box_version = "1.0"
    MACHINES.each do |boxname, boxconfig|
  
        config.vm.define boxname do |box|
  
            box.vm.box = boxconfig[:box_name]
            box.vm.host_name = boxname.to_s
  
            #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset
  
#            box.vm.network "private_network", ip: boxconfig[:ip_addr]
            # Additional network config if present
               if boxconfig.key?(:net)
                  boxconfig[:net].each do |ipconf|
                  box.vm.network "private_network", ipconf
                end
               end
  
            box.vm.provider :virtualbox do |vb|
                    vb.customize ["modifyvm", :id, "--memory", "1024"]
                    needsController = false
            boxconfig[:disks].each do |dname, dconf|
                unless File.exist?(dconf[:dfile])
                  vb.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
                                  needsController =  true
                            end
  
            end
                    if needsController == true
                       vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
                       boxconfig[:disks].each do |dname, dconf|
                           vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
                       end
                    end
            end
  
#        box.vm.provision "shell", inline: <<-SHELL
#            mkdir -p ~root/.ssh
#           cp ~vagrant/.ssh/auth* ~root/.ssh
#            yum install -y mdadm smartmontools hdparm gdisk
#          SHELL
         

         config.vm.synced_folder ".", "/provision",  
          type: "rsync",
          rsync_auto: "true",
          rsync_exclude: [".git/",".vagrant/",".gitignore","Vagrantfile"]
        # id: "vagrant"
        #  rsync__args: ["--verbose", "--rsync-path='sudo rsync'", "--archive", "--delete", "-z"]
       # config.vm.provision "shell", path: "provision/rootmv1.sh" 
       # config.vm.provision "shell", path: "provision/rootmv2.sh" 
       # config.vm.provision "shell", path: "provision/varmv.sh" 
       # config.vm.provision "shell", path: "provision/homemv.sh" 
 
      end
    end
  end
  
