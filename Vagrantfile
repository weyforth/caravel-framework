# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.network "private_network", type: "dhcp"
    
    config.vm.define :laravel5 do |lv5_config|
        lv5_config.vm.box = "ubuntu/trusty32"
        lv5_config.vm.box_url = "https://vagrantcloud.com/ubuntu/trusty32"
        lv5_config.ssh.forward_agent = true
        lv5_config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

        # This will give the machine a static IP uncomment to enable
        # lv5_config.vm.network :private_network, ip: "192.168.56.101"
        
        lv5_config.vm.network :forwarded_port, guest: 80, host: 8888, auto_correct: true
        lv5_config.vm.network :forwarded_port, guest: 3306, host: 8889, auto_correct: true
        lv5_config.vm.network :forwarded_port, guest: 5432, host: 5433, auto_correct: true
        lv5_config.vm.synced_folder "./", "/var/www", id: "vagrant-root", nfs: true
            # owner: "vagrant",
            # group: "www-data"
        lv5_config.vm.provision :shell, :inline => "echo \"Europe/London\" | sudo tee /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata"

        lv5_config.vm.provider :virtualbox do |v|
            v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]

            host = RbConfig::CONFIG['host_os']

            # Give VM 1/4 system memory & access to all cpu cores on the host
            if host =~ /darwin/
                cpus = `sysctl -n hw.ncpu`.to_i
                # sysctl returns Bytes and we need to convert to MB
                mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
            elsif host =~ /linux/
                cpus = `nproc`.to_i
                # meminfo shows KB and we need to convert to MB
                mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
            else # sorry Windows folks, I can't help you
                cpus = 2
                mem = 1024
            end

            v.customize ["modifyvm", :id, "--memory", mem]
            v.customize ["modifyvm", :id, "--cpus", cpus]
            # v.customize ["modifyvm", :id, "--memory", "512"]
        end

        #lv5_config.vm.provision :puppet do |puppet|
        #    puppet.manifests_path = "puppet/manifests"
        #    puppet.manifest_file  = "phpbase.pp"
        #    puppet.module_path = "puppet/modules"
        #    #puppet.options = "--verbose --debug"
        #end

        lv5_config.vm.host_name = File.basename(ENV['PWD']) + ".local"

        #lv5_config.vm.provision :shell, :path => "puppet/scripts/enable_remote_mysql_access.sh"

        lv5_config.vm.provision :shell, :path => "bootstrap.sh"
    end
end