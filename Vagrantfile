Vagrant.configure("2") do |config|

  config.ssh.insert_key = false

  # requires vagrant-disksize plugin - https://github.com/sprotheroe/vagrant-disksize
  config.disksize.size = '40GB'

  config.vm.synced_folder '.', '/vagrant', type: 'nfs', disabled: true

  config.vm.define "virtualbox" do |virtualbox|
    virtualbox.vm.hostname = "devbox"
    virtualbox.vm.box = "ubuntu/bionic64"

    virtualbox.vm.network :private_network, ip: "172.16.3.2",  nic_type: "virtio"
    virtualbox.vm.network "forwarded_port", guest: 8080, host: 8080
    virtualbox.vm.network "forwarded_port", guest: 8000, host: 8000
    virtualbox.vm.network "forwarded_port", guest: 8181, host: 8181
    virtualbox.vm.network "forwarded_port", guest: 3000, host: 3000
    virtualbox.vm.network "forwarded_port", guest: 3001, host: 3001
    virtualbox.vm.network "forwarded_port", guest: 9000, host: 9000
    virtualbox.vm.network "forwarded_port", guest: 1337, host: 1337
    virtualbox.vm.network "forwarded_port", guest: 5432, host: 5432

    config.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 8192
      v.cpus = 4
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
    end

    virtualbox.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get upgrade
      apt-get install -y \
        git \
        tig \
        vim \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common \
        zsh \
        build-essential \
        tmux \
        libnss3-tools \
        jq

      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
      add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      apt-get update
      apt-get -y install docker-ce docker-ce-cli containerd.io
      curl -sL "https://github.com/docker/compose/releases/download/1.28.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      chmod +x /usr/local/bin/docker-compose

      usermod -aG docker vagrant
      usermod -s /usr/bin/zsh vagrant

      (su vagrant /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" || exit 0)

      su vagrant -c "curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash"

      curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
      echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
      sudo apt update
      sudo apt install --no-install-recommends yarn

      su vagrant -c "mkdir -p /home/vagrant/projects"

      echo "fs.inotify.max_user_watches = 1048576" | sudo tee --append /etc/sysctl.conf > /dev/null

      su vagrant -c "if [ ! -f ~/.ssh/id_ed25519_devbox ]; then ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_devbox -P ''; fi"
      su vagrant -c "if [ ! -f ~/.ssh/config ]; then echo 'Host *' >> ~/.ssh/config; ; echo '  IdentityFile ~/.ssh/id_ed25519_devbox' >> ~/.ssh/config; fi"
      echo "\n\n---\n"
      echo "A public key has been created for you for this box, please add it to Gitlab/Github/Bitbucket accounts"
      echo "Or you can import your keys into the box"
      echo "\n---\n"
      echo "The key: $(cat /home/vagrant/.ssh/id_ed25519_devbox.pub)"
      echo "\n\nEnjoy!"
    SHELL

    virtualbox.vm.provision "file", source: "./.zshrc", destination: "/home/vagrant/.zshrc"
  end
end
