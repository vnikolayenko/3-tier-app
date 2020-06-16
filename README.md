# 3-tier-app
Deployment via Ansible by using Nginx, Postrgre, Pythom code on Ubuntu server 18
---
My Steps
---
# 1. Configure Ubuntu Server
* 1. Install server (VirtualBox, Docker)
* 2. Connect via ssh(from localhost) and do such commands
*  Generate SSH keyssh-keygen -t rsa -b 4096 -C "your_email@domain.com"
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
* 4. Create Config for NGINX
* Create conf file "static_site.cnf"
User "vlad" hardcoded!
* Create dir "/static-site-src" with website pages "index.html"
* Create Playbook "nginx.yml"
Path to user "vlad" dir hardcoded by user name "vlad"!
* Start playbook with command
``` ansible-playbook -i inventory.cfg  --limit 192.168.56.104 nginx.yml -K -u vlad  ```
* Check it out with curl
``` curl http://ip_of_server ```
# 4. Create Config for Postgres
* 1. Write playbook for posgres "postgres_playbook.yml"
* 2. Write "vars/main","roles/createdb/handlers/main.yml", "roles/createdb/tasks/main.yml"
* 3. Start playbook
```  ansible-playbook postgres_playbook.yml -K -u vlad ``` 
On steps of "Ensure database is created" it is failed, so I edit field in "/etc/postgresql/10/main/pg_hba.conf" file on Ubuntu server. I connect via SSH and change line with "local   all             postgres                                peer" to line "local   all             postgres                                trust". It's give be ability to go ahead. But in next point it failed "Ensure user has access to the database", so I commented it. How I try to solve the problem?
* *  Change those line to md5 and try to sent with username encrypted password but failed.
* * Try to create postgres user via Ansible(for peer logging), but also failed. So I thought to first install Postgre and Python. Maybe later I solve this problem.
So, now, when command executed, I can be ensured that Postgre installed and db created, but without user.  
# 5. Create Config to Python
* At first, I try to repeat steps to get root SSH connection, but can't copy id. Why? For password was permission denied, so I tried to edit "sshd_config" and restart ssh servise - it's not worked.
* I losed ability to install packages via ansible, so all neede packages I installed manually.
* Write playbook "python_wsgi.yml", but can't test it properly.
# 6. Create systemd file
* Write systemd file "systemd/myproject.service" for automatic start of uWSGI wich serve Flask app after start of server.
* Write playbook "systemd.yml" for coping service file and activating uWSGI service start after system booting.  
