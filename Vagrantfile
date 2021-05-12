# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"
  config.vm.disk :disk, size: "200GB", primary: true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = "32768"
    vb.cpus = "8"

    # Turn on nested virtualization support
    vb.customize ["modifyvm", :id, "--nested-hw-virt", "on"]

    # enable DNS proxing through host
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  config.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"

  # Install required software for build
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update

    # install bazel
    apt-get install curl gnupg -y
    curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor > bazel.gpg
    mv bazel.gpg /etc/apt/trusted.gpg.d/
    echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list
    apt-get update
    apt-get install bazel-3.4.1 -y
    ln -s /usr/bin/bazel-3.4.1 /usr/bin/bazel

    # install python
    apt-get install software-properties-common -y
    apt-get install python -y

    # install K3s
    curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.18.6+k3s1" sh -s - --no-deploy traefik

    # install Helm
    curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
    apt-get install apt-transport-https -y
    echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    apt-get update
    apt-get install helm -y

    # install docker
    apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common -y
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt-get update
    apt-get install docker-ce docker-ce-cli containerd.io -y
    usermod -aG docker vagrant

    # install awscli
    apt-get install awscli -y
  SHELL
end
