# Auto deployment Website and Monitoring
![Vagrant](https://img.shields.io/badge/Vagrant-1868F2?logo=vagrant&logoColor=white)
![Ansible](https://img.shields.io/badge/Ansible-EE0000?logo=ansible&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Ubuntu-E95420?logo=ubuntu&logoColor=white)
## About
This project includes an **Ansible Playbook** and a **Vagrantfile** for automatic deployment of a website on Nginx with monitoring via Grafana and Prometheus.

### Setup
Vagrant uses ubuntu/jammy64, and includes a base setup with ufw, git, curl, vim, wget, tar, Docker and Docker Compose.   

### Monitoring
All monitoring tools are running via Docker Compose in containers.   
**Prometheus** - includes Node Exporter and Nginx Prometheus Exporter.   
**Grafana** - this repo includes 2 dashboards: one for Node Exporter and another one for the Nginx Prometheus Exporter.   
**Access for metrics** - access to Nginx metrics is allowed only from the localhost of the web server and the monitoring VM for security purposes. The web server and monitoring VM use a subnet mask of 255.255.255.252 (/30) allowing only two IPs: one for the web server and one for the monitoring VM. Localhost access is also allowed.       

### Ip Configuration
Vagrantfile and inventory.yml include two VMs   
1. **Monitor** - Grafana and Prometheus, **VM's IP 192.168.56.10**   
   - Grafana - **192.168.56.10:3000**   
   - Prometheus - **192.168.56.10:9090**  
   - Node Exporter - **192.168.56.10:9100**  
   - Nginx prometheus exporter - **192.168.56.10:9113**       
2. **Web-Server** - Nginx, **VM's IP 192.168.56.11**   
   - WebSite - **192.168.56.11:8081**

## Setup WebSite
### Requirements
Download [Vagrant](https://developer.hashicorp.com/vagrant/install):
```bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant

vagrant --version

```

Install [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible):
```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
ansible --version
```
Install [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads):
```bash
sudo apt remove vagrant -y
wget https://releases.hashicorp.com/vagrant/2.4.1/vagrant_2.4.1-1_amd64.deb
sudo apt install ./vagrant_2.4.1-1_amd64.deb
echo "deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
sudo apt update
sudo apt install -y virtualbox-7.0
wget https://download.virtualbox.org/virtualbox/7.0.20/Oracle_VM_VirtualBox_Extension_Pack-7.0.20.vbox-extpack
sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-7.0.20.vbox-extpack
vagrant plugin install vagrant-vbguest
vagrant plugin list
VBoxManage --version
```

### Add index.html
Copy this repository to any folder and put all your website files into the **website** folder.   The main file must be named **index.html**.   
Open a terminal in this folder and run:
```bash
Vagrant up
```
P.S. The repo includes index.html and styles.css as examples for testing.

## Setup Monitoring
1. Open **http://yourMonitorVmIP:3000** in your browser. Default credentials are admin / admin   
2. Add data source: Prometheus, enter **http://yourMonitorVmIP:9090**, then click Test & save.   
3. Import new dashboards – just copy the JSON files from this repo and select the Prometheus data source.

To view metrics directly in Prometheus, open **http://yourMonitorVmIP:9090**, and go to "Status > Target 
health".

## Preview Dashboard
### Node Exporter
![Node Exporter](https://github.com/gwill1337/Images/blob/main/Auto_Deploy_WebSRV_Images/Node_Exporter_Dashboard.png?raw=true)
### NGINX Prometheus Exporter
![NGINX Prometheus Exporter](https://github.com/gwill1337/Images/blob/main/Auto_Deploy_WebSRV_Images/NGINX_Prometheus_Exporter_Dashboard.png?raw=true)
### Trobleshooting
If you have any issues with vagrant up, make sure that "kvm_intel", "kvm_amd", and "kvm" are disabled:   
Commands for disable it:
```bash
sudo modprobe -r kvm_intel
sudo modprobe -r kvm_amd
sudo modprobe -r kvm
```
Another issue may occur if you run vagrant up without root privileges. Try running it as root:
```bash
vagrant destroy -f
sudo vagrant up
```
One more possible issue appears if you try to run Vagrant up on a host that already has a running VM.
Make sure that **Hyper-V** and **Virtual Machine Platform** are disabled in Windows Features.
Then, enable **Nested VT-x/AMD-V** in your VM provider settings.

