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
        # install apt repositories
        curl -sSL https://deb.nodesource.com/setup_8.x | sudo -E bash -
        sudo add-apt-repository -y ppa:ondrej/php
        sudo add-apt-repository -y ppa:certbot/certbot
        sudo apt-get -y update

        # install different php versions
        for VERSION in "5.6" "7.0" "7.1"; do
            sudo apt-get -y install \
            php$VERSION php$VERSION-xml php$VERSION-curl \
            php$VERSION-soap php$VERSION-mysql php$VERSION-fpm
        done

        # install apache
        sudo apt-get -y install apache2 libapache2-mod-fastcgi cronolog

        # install letsencrypt
        sudo apt-get -y install python-certbot-apache

        # install nodejs and npm
        sudo apt-get -y install build-essential nodejs

        # install dev tools
        sudo npm install -g gulp knex
        sudo apt-get -y install sqlite3

        # enable proxy
        sudo a2enmod rewrite proxy proxy_fcgi
        sudo service apache2 restart

        # cleanup apt packages
        sudo apt-get -y autoremove

        # install packages
        AVANTI_PATH="/home/vagrant/avanti/"
        for MODULE in "avanti-core" "avanti-cli" "avanti-api"; do
            if [ -d "/home/vagrant/.config" ]; then
                sudo chown -R $USER:$(id -gn $USER) /home/vagrant/.config
            fi
            cd $AVANTI_PATH$MODULE
            npm install
            sudo npm link
            if [ "$MODULE" != "avanti-core" ]; then
                sudo npm link avanti-core
            fi
        done
    SCRIPT

    config.vm.provision "shell", inline: $script, privileged: false

end
