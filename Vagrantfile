# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network :private_network, ip: "192.168.66.66"
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  config.vm.synced_folder './','/vagrant'
  config.vm.synced_folder 'www/', '/var/www/site', owner: "www-data", group: "vagrant"

  config.vm.provider :virtualbox do |vb|
    vb.name = "HHVM"
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    vb.customize ["modifyvm", :id, "--ostype", "Ubuntu_64"]
    vb.customize ["modifyvm", :id, "--cpus", "2"]
  end

  config.vm.provision "shell", inline: <<-shell
    apt-get install software-properties-common
    apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0x5a16e7281be7a449
    add-apt-repository "deb http://dl.hhvm.com/ubuntu $(lsb_release -sc) main"
    apt-get update
    apt-get install hhvm -y --force-yes
    apt-get install python-software-properties  -y --force-yes
    add-apt-repository ppa:mapnik/boost
    add-apt-repository ppa:nginx/stable
    apt-get update
    apt-get install nginx -y --force-yes
    apt-get install screen vim -y --force-yes
    debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password pa$$'
    debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password pa$$'
    apt-get install mysql-server -y --force-yes

    sudo chown vagrant /etc/hhvm
    #sudo cp /vagrant/conf/config.hdf /etc/hhvm/my-config.hdf
    sudo cp /vagrant/conf/php.ini /etc/hhvm/my-php.ini
    sudo rm /etc/nginx/sites-enabled/default
    sudo cp /vagrant/conf/nginx-fastcgi /etc/nginx/sites-available/nginx-fastcgi
    sudo ln -s /etc/nginx/sites-available/nginx-fastcgi /etc/nginx/sites-enabled/nginx-fastcgi
    sudo service nginx restart

    #hhvm -m daemon -c /etc/hhvm/my-php.ini -v Eval.EnableXHP=1

  shell
end
