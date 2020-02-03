Vagrant.configure("2") do |config|
  

  config.ssh.insert_key = false

  config.vm.synced_folder '.', '/vagrant', type: 'nfs', disabled: true

  config.vm.define "virtualbox" do |virtualbox|
    virtualbox.vm.hostname = "devbox"
    virtualbox.vm.box = "ubuntu/bionic64"

    virtualbox.vm.network :private_network, ip: "172.16.3.2",  nic_type: "virtio"

    config.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 8192
      v.cpus = 4
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
    end

    virtualbox.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y tig vim  apt-transport-https ca-certificates curl gnupg-agent software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
      add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      apt-get update
      apt-get -y install docker-ce docker-ce-cli containerd.io
      curl -sL "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      chmod +x /usr/local/bin/docker-compose

      usermod -aG docker vagrant

      apt-get -y install zsh
      usermod -s /usr/bin/zsh vagrant 
      su vagrant /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

    SHELL


  end
end
