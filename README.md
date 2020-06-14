# 3-tier-app
Deployment via Ansible by using Nginx, Postrgre, Pythom code on Ubuntu server 18
---
My Steps
---
# 1. Configure Ubuntu Server
* 1. Install server (VirtualBox, Docker)
* 2. Connect via ssh(from localhost) and do such commands
*  Generate SSH keys 
``` ssh-keygen -t rsa -b 4096 -C "your_email@domain.com" ```
* Install Python Module
``` sudo apt-get install python-minimal -y ```
# 2. Set up local machine
* 1. Get SSH public key
``` ssh-copy-id remote_username@server_ip_address ```
* 2. Install Ansible
``` sudo apt-get install -y ansible ```
* Configure inventory file 
``` sudo touch /etc/ansible/hosts ``` with 
```
[web]
ip_of_server 
```
Ip hardcoded!
* Test connectivity
``` ansible web -m ping -u [user] -vvvv ```
# 3. Set up ansible nginx configuration
* 1. Make inventory file
``` vim inventory.cfg ```
``` 
[web]
ip_of-server

[web:vars]
ansible_python_interpreter=/usr/bin/python3 
```
* 2. Make Ansible playbook
``` vim nginx_install.yml ```
```
---
- hosts: all
  tasks:
    - name: ensure nginx is at the latest version
      apt: name=nginx state=latest
    - name: start nginx
      service:
          name: nginx
          state: started
``` 
* 3. Start install NGINX
``` ansible-playbook -i inventory.cfg nginx_install.yml -b -u vlad -K ``` while starting enter user password
* Test to get HTML output
``` curl http://[ip_of_server] ```
* Stop Nginx Service
``` ansible-playbook -i inventory.cfg nginx_uninstall.yml -b -u vlad -K ``` while starting enter us
er password 
* 4. Create Cinfig for NGINX
* Create conf file "static_site.cnf"
User "vlad" hardcoded!
* Create dir "/static-site-src" with website pages "index.html"
* Create Playbook "nginx.yml"
Path to user "vlad" dir hardcoded by user name "vlad"!
* Start playbook with command
``` ansible-playbook -i inventory.cfg  --limit 192.168.56.104 nginx.yml -K -u vlad  ```
* Check it out with curl
``` curl http://ip_of_server ```

