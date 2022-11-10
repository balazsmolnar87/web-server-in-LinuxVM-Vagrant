## This project is to practice DevOps skills.

### I was following this tutorial: Run a web server in a Linux VM with Vagrant [Learning Project] 
### www.tutorialworks.com/linux-vm-vagrant/
Made by Tom Donohue

---

We are going to use Vagrant and Virtualbox to deploy an Apache web server on an Ubuntu distribution inside our VM.

### Install softwares if needed

Install VirtaulBox www.virtualbox.org

Install Vagrant www.vagrantup.com

---

## Create a virtual machine

### Create and start an Ubuntu Linux VM with Vagrant

In a terminal create a folder for your project. This folder will also act as a shared folder between your host and guest machine. Inside your project folder type:
```
vagrant init ubuntu/jammy64
```
```
vagrant up
```
Vagrant will download the Ubuntu 22.04 box, it might take some time. When it finishes you may have a look inside the virtual machine, type:
```
vagrant ssh
```
Type exit to exit the virtual machine.

---

## Install the web server

### Install Apache in the Ubuntu VM

Inside the VM type:
```
sudo apt update
```
```
sudo apt install apache2
```
You may run:

```
systemctl status apache2
```

to confirm the apache daemon is installed and running. Type:

```
curl -v localhost:80
```

and you should get back a HTML response.

In order to reach our VM from our host machine's web browser I chose to forward a port, but you may use other methods as well.

Open the Vagrantfile inside your project folder. Search for lines describing port forwarding and uncomment out the lines, or insert:

```
config.vm.network "forwarded_port", guest: 80, host: 8080
```

Run:

```
vagrant reload
```

command to reload the Vm with the changes.

With this we will be ablo to reach the apache web server wich is default listening on port 80 on port 8080 on our host machine's web browser. Now save the Vagrantfile. You might get an error saying the port 8080 is already being used by an other program on your machine. You may want to end that service or alloce some other port in the Vagrantfile.

---

## Test the setup

### Access the website from a web browser

Open a browser and type ```localhost:8080``` inside the address bar and press enter. You should be able to see apache2's default page.

---

## Replace the default website

### Copy the web content inside the shared folder.

Create a folder for the web content:

```
mkdir webcontent
```

Copy your content on your host machine to this folder.

---

## Automate provisioning

### Add provisioning steps fro the VM

We will include the copying of our web content to apache's default folder making sure our content loads when we ask the web server. Open Vagrantfile and edit/uncomment the last block of lines to look like this:

```
  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get install -y apache2
     cp -r /vagrant/webcontent/* /var/www/html/
   SHELL
end
```
Check if Vagrantfile is looking good

```
vagrant validate
```
Should come back with something like: Vagrantfile validated successfully.

Run vagrant destroy to delete the existing virtual machine.
```
vagrant destroy
```
Now finally bring up the machine with the automatic provisioning code:
```
vagrant up
```
