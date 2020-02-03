# Devbox for Mac

This project will setup a Vagrant environment with a ready to use Docker environment. 

### Why

Docker for Mac is horrible. Slow, file permissions management is wrong, docker configuration is likely to break for people using a Linux system. Basically, you don't have the correct Docker experience on a Mac.

### Solution

Use a real Linux system from within your Mac: welcome (back) Vagrant. The current solution does not used shared folder from Vagrant. But rely on built-in features of IDE:

- vscode: https://code.visualstudio.com/Download with Remote SSH: https://code.visualstudio.com/docs/remote/ssh
- IntellIJ based editor: Use the sync options.

### Installation

    brew cask install virtualbox
    brew cask install vagrant

    mkdir -p Documents/devbox
    cd Documents/devbox

    curl -sL https://raw.githubusercontent.com/ekino/devbox/master/Vagrantfile -o Vagrantfile
    
    vagrant up

### Manual Post-Operations

#### Configure your git information

    git config --global user.name "Your Name Here"
    git config --global user.email "your_email@youremail.com"

#### Generate a dedicated SSH Key

    ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_devbox_$(date +%Y-%m-%d) -C "Ekino DevBox key for ENTER YOUR NAME"
    
> Using a dedicate key helps to avoid avoid one key for all usages. You can have a key for pushing code to repositories. And a key (on the host) to connect to servers. So if a dependency is bloated with a malware, your servers' keys are not exposed.

> Don't forget to share your public key to services that may required it.

#### Accessing the box

Copy the output of `vagrant ssh-config --host devbox` into your `~/.ssh/config` file. 

You should be able to connect to the box using `ssh devbox`.

#### Importing projects

    rsync -av ~/projects-folder devbox:~/projects

#### Accessing projects

By default the box expose the following ports: 8080, 8181, 3000, 3001, 1337 and 5432.

You can add new ports by editing the `Vagrantfile`. Don't forget to run `vagrant reload` to apply the change.

### VSCODE Integration

#### Configuring VSCode

- Make sure you have vscode installed on your laptop. 
- Install the vscode extension: https://aka.ms/vscode-remote/download/extension
- Make sure you can connect to the box using `ssh devbox`
- Remote-SSH: Connect to Host from the Command Palette and enter `devbox`
- You can now list projects available on the box.

#### Configuring VSCode's extensions

- By default, VSCode install extensions on the host. Now you have an second option to also install an extension on the box.
- So, you can loop through your installed extensions and click on `Install on SSH...`

### References

- https://infosec.mozilla.org/guidelines/openssh
- https://code.visualstudio.com/docs/remote/ssh
