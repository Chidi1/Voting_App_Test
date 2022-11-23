Example Voting App
=========

A simple multi-tier voting application running on a Linux VM (Ubuntu) managed by Vagrant.  

Tools used
--------------- <br>  

| Category | Tools |  
| :----: | :----: |  
| Virtulization | VirtualBos |  
| Automation | Vagrant |  
| Operating System | Linux (Ubuntu 20.04) |  
| Web Server | Apache HTTP Server |  
| Ochestrator | Docker-compose |  


Getting started
---------------  

## Downloads:
![Install VirtualBox](https://www.virtualbox.org/)
![Install Vagrant](https://www.vagrantup.com/)  

## Create and start an Ubuntu Linux VM with Vagrant  

*Open your terminal (e.g. a Mac Terminal or Windows Command Prompt). Create a new directory and cd into it:    
```
makdir banque_test
cd banque_test
```  
*Use the vagrant init command to initialise a new vagrant machine.  
```
vagrant init ubuntu/jammy64  
```
*Boot up the virtual machine with:  
```
vagrant up
```
*Go inside the VM with this command:
```
vagrant up  
```  

## Install the web server (Apache)  

*Install Apache using the following commands:
```
sudo apt update 
sudo apt install apache2  
```  
*Check if Apache is running
```  
systemctl status apache2  
```  

## Setup network to isolate the traffic of the client-facing applications from the backend applications  

*Find the file banque_test/Vagrantfile:  
```
cd banque_test
sudo vim Vagrantfile
```
*Add this line to set the network configuration to static IP and save:  
```
config.vm.network "private_network", ip: "192.168.33.10"  
```  

*Reload and SSH into VM:
```
vagrant reload
vagrant ssh
```  

*Install dependencies:
```
sudo apt install docker.io
```  
*Add current user to docker group:
```  
sudo usermod -aG docker $(whoami)
```  
*Start docker:
```  
systemctl start docker
```  

## Cd into the default Ubuntu document root (/var/www/html) and add application files

*Run the following commands to add files and spin up the app:
```  
cd /var/www/html
git clone https://github.com/Chidi1/example-voting-app.git
cd example-voting-app
docker-compose up -d
```  

## Test the application  

*Visit the following URLs:
```  
The app will be running at http://192.168.33.10:5000/ 
The results will be at http://192.168.33.10:5001/ 
```  

Architecture
-----

![Architecture diagram](architecture.png)

* A front-end web app in [Python](/vote) or [ASP.NET Core](/vote/dotnet) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) or [NATS](https://hub.docker.com/_/nats/) queue which collects new votes
* A [.NET Core](/worker/src/Worker), [Java](/worker/src/main) or [.NET Core 2.1](/worker/dotnet) worker which consumes votes and stores them in…
* A [Postgres](https://hub.docker.com/_/postgres/) or [TiDB](https://hub.docker.com/r/dockersamples/tidb/tags/) database backed by a Docker volume
* A [Node.js](/result) or [ASP.NET Core SignalR](/result/dotnet) webapp which shows the results of the voting in real time


Notes
-----

The voting application only accepts one vote per client. It does not register votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple 
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to 
deal with them in Docker at a basic level. 
