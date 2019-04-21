# -*- mode: ruby -*-
# vi: set ft=ruby :

# Every Vagrant development environment requires a box. You can search for
# boxes at https://atlas.hashicorp.com/search.
BOX_IMAGE = "centos/7"
NODE_COUNT = 2

Vagrant.configure("2") do |config|
  config.vm.define "master" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "master"
    subconfig.vm.network :private_network, ip: "10.0.0.10"
    
  end
  
  
  (1..NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.network :private_network, ip: "10.0.0.#{i + 10}"
      
    end
  end

  
  config.vm.provision "file", source: Dir.getwd + "/kubeconfig.repo", destination: "/vagrant"
  config.vm.provision "file", source: Dir.getwd + "/cni-plugins-amd64-v0.7.4.tgz", destination: "/vagrant"
  config.vm.provision "file", source: Dir.getwd + "/crictl-v1.14.0-linux-amd64.tar.gz", destination: "/vagrant"
  config.vm.provision "file", source: Dir.getwd + "/epel-release-latest-7.noarch.rpm", destination: "/vagrant"
  config.vm.provision "file", source: Dir.getwd + "/99-kubernetes-cri.conf", destination: "/vagrant"

  config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
    end


  # Install avahi on all machines  
  config.vm.provision "shell", inline: <<-SHELL
    #vag  yum install -y epel-release
    echo "nameserver 8.8.8.8" > /etc/resolv.conf
    sudo rpm -ivh /vagrant/epel-release-latest-7.noarch.rpm
    
    modprobe overlay
    modprobe br_netfilter
    sudo cp /vagrant/99-kubernetes-cri.conf /etc/sysctl.d/

    # Setup required sysctl params, these persist across reboots.
    sysctl --system

    # Install prerequisites
    yum-config-manager --add-repo=https://cbs.centos.org/repos/paas7-crio-311-candidate/x86_64/os/

    # Install CRI-O
    yum install --nogpgcheck cri-o -y 
    yum install podman -y
    systemctl start crio
    rm -rf /etc/cni/net.d/*crio-bridge.conf 
    rm -rf /etc/cni/net.d/*loopback.conf 
    rm -rf /etc/yum.repos.d/*cbs*
    # Set SELinux in permissive mode (effectively disabling it)
    setenforce 0
    
    cp /vagrant/kubeconfig.repo /etc/yum.repos.d/
   
    sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
    RELEASE="$(curl -sSL https://dl.k8s.io/release/stable.txt)"
    echo $RELEASE
    swapoff -a
    yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
    
    systemctl enable --now kubelet
  SHELL
end
