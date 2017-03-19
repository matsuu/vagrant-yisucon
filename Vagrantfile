# -*- mode: ruby -*-
# vi: set ft=ruby :


image_ip = "192.168.33.10"
bench_ip = "192.168.33.11"

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/centos-7.2"
  #config.vm.box = "centos/7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL

  config.vm.provision :shell, inline: <<-SHELL
    set -e
    yum install -y gcc git
    test -e yisucon || git clone https://github.com/yahoojapan/yisucon.git
  SHELL

  config.vm.define :image do |web|
    #web.vm.provider :virtualbox do |vb|
    #  vb.memory = "1024"
    #end
    web.vm.network "private_network", ip: image_ip
    #web.vm.network "forwarded_port", guest: 80, host: image_port
    web.vm.provision :shell, inline: <<-SHELL
      set -e
      (
        cd yisucon/provisioning
        git pull
        ./provision.sh development isucon
      )
      rm -rf yisucon
    SHELL
  end

  config.vm.define :bench do |bench|
    #bench.vm.provider :virtualbox do |vb|
    #  vb.memory = "1024"
    #end
    bench.vm.network "private_network", ip: bench_ip
    #bench.vm.network "forwarded_port", guest: 80, host: bench_port
    bench.vm.provision :shell, inline: <<-SHELL
      set -e
      export PATH="/usr/local/bin:$PATH"
      yum install -y mariadb-server
      systemctl start mariadb
      systemctl enable mariadb
      (
        cd yisucon/provisioning
        git pull

        cat ../benchmarker/init.sql | sed -e "s/DATETIME/TIMESTAMP/" | mysql -u root

        sed -i \
          -e "/start_date/s@CHANGE HERE@1970/01/01 00:00:00@" \
          -e "/end_date/s@CHANGE HERE@2038/01/19 03:14:07@" \
          -e "/portal_host/s@CHANGE HERE@127.0.0.1@" \
          -e "/db_host/s@CHANGE HERE@127.0.0.1@" \
          -e "/db_user/s@CHANGE HERE@root@" \
          -e "/db_pass/s@CHANGE HERE@@" \
          environments/production.json

        sed -i \
          -e "s@sudo @sudo env PATH=\"$PATH\" @" \
          -e "/npm install/a yarn install" \
          -e "s/npm install$/npm install -g yarn/" \
          -e "s/build:prod:ngc/build/" \
          -e "s@/etc/environment@/etc/portal_environment@" \
          cookbooks/portal/recipes/default.rb

        ./provision.sh production portal 

        sed -i \
          -e "/\[Unit\]/a Wants=portal.service" \
          -e "s@/etc/environment@/etc/benchmark_environment@" \
          cookbooks/benchmarker/recipes/default.rb

        ./provision.sh production benchmarker
      )
      rm -rf yisucon
    SHELL
  end
end
