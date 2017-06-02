# -*- mode: ruby -*-
# vi: set ft=ruby :

VMname = File.basename(Dir.getwd) + "-vagrant"

Vagrant.configure(2) do |config|

    config.vm.box = "dattn/ubuntu16"

    config.vm.network :forwarded_port, guest: 80, host: 8080
    config.vm.network :forwarded_port, guest: 3000, host: 3000

    config.vm.synced_folder ".", "/home/vagrant/avanti/",
        owner: "vagrant",
        group: "vagrant",
        mount_options: ["dmode=775,fmode=775"]

    config.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
        vb.cpus   = 2
        vb.name   = "#{VMname}"

        # see http://datasift.github.io/storyplayer/v2/tips/vagrant/speed-up-virtualbox.html
        # change the network card hardware for better performance
        vb.customize ["modifyvm", :id, "--nictype1", "virtio" ]
        vb.customize ["modifyvm", :id, "--nictype2", "virtio" ]

        # suggested fix for slow network performance
        # see https://github.com/mitchellh/vagrant/issues/1807
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end

    # Set name of VM
    config.vm.define "#{VMname}" do |vb|
    end

    $script = <<-SCRIPT
        curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
        sudo add-apt-repository -y ppa:ondrej/php
        sudo add-apt-repository -y ppa:certbot/certbot
        sudo apt-get -y update
        sudo apt-get -y dist-upgrade

        # install different php versions
        sudo apt-get -y install php5.6 php5.6-xml php5.6-curl php5.6-soap php5.6-mysql php5.6-fpm
        sudo apt-get -y install php7.0 php7.0-xml php7.0-curl php7.0-soap php7.0-mysql php7.0-fpm
        sudo apt-get -y install php7.1 php7.1-xml php7.1-curl php7.1-soap php7.1-mysql php7.1-fpm

        # install apache
        sudo apt-get -y install apache2 libapache2-mod-fastcgi cronolog

        # install letsencrypt
        sudo apt-get -y install python-certbot-apache

        # install nodejs and npm
        sudo apt-get -y install build-essential nodejs
        sudo npm -g install npm@4

        # install dev tools
        sudo npm install -g gulp knex
        sudo apt-get -y install sqlite3

        # enable proxy
        sudo a2enmod rewrite proxy proxy_fcgi
        sudo service apache2 restart

        # cleanup apt packages
        sudo apt-get -y autoremove

        # install packages
        cd /home/vagrant/avanti/avanti-core
        npm install
        gulp build
        cd /home/vagrant/avanti/avanti-cli
        npm install
        gulp build
        cd /home/vagrant/avanti/avanti-api
        npm install
        gulp build
        sudo npm -g install /home/vagrant/avanti/avanti-core /home/vagrant/avanti/avanti-cli /home/vagrant/avanti/avanti-api
    SCRIPT

    config.vm.provision "shell", inline: $script, privileged: false

end
