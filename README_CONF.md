---
# Setuping steps
---
# 0. Clone repo to your local machine.
# 1. Create VM or Docker container with Ubuntu 18.04.
* Get Ip adress
``` ip a ```
# 2. Connect via SSH to remote host using ip adress from previous step.
``` ssh [user]@[ip] ```
* Generate SSH keys 
``` ssh-keygen -t rsa -b 4096 -C "your_email@domain.com" ```
* Install Python module wich needed for future ansible proper setuping of system.
# 3. Set up local machine 

* 1. Get SSH public key for not root user
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

# 4. Set up ansible nginx configuration
* Before start command change user dir and server_ip in "static_site.cnf" and "nginx.yaml"
* Start playbook with command
``` ansible-playbook -i inventory.cfg  --limit [ip_of server] nginx.yml -K -u [not_root_user]  ``
` 
* Check it out with curl
``` curl http://[ip_of_server] ```
If all worked go ahead and stop for a while nginx service
* Before start command change user dir and server_ip in "static_site.cnf" and "nginx.yaml"
* ``` ansible-playbook -i inventory.cfg nginx_uninstall.yml -b -u [user] -K ```
# 5. Create Config for Postgres
* Connect via SSH to remote server and change "/etc/postgresql/10/mai
n/pg_hba.conf" file in line "local   all       postgre    trust". It gave us possibility to ensure after starting playbook that db is created.So, now, when command executed, I can be ensured that Postgre installed and db created, but witho
ut user.
* As always don't forget to change on your own remote server user and ip in such files "postgres_playbook.yml", "vars/main","roles/createdb/handlers/main.yml", "roles/createdb/tasks/main.yml".
```  ansible-playbook postgres_playbook.yml -K -u [user] ```

# 6. Create Config to Python
* Before start change user and server_ip in in file "python_wsgi"
* Start ansible playbook for copying files
``` ansible-playbook python_wsgi.yml -K -u [user] ```
* As I can't do some steps with root creds, so you need to install manually packages while your connected via SSH to remote server.
* Install components 
``` apt-get update && apt-get install python3-pip python3-dev build-essential libssl-dev libffi-dev python3-setuptools ```
* Conf VirtEnv
``` apt-get install python3-venv && mkdir myproject && cd myproject && python3.6 -m venv myprojectenv && source myprojectenv/bin/activate ```
* Install Flask and uWSGI
``` pip install wheel && pip install uwsgi flask ```
* Enable port 5000 and start App
``` sudo ufw allow 5000 ```
# 7. Create systemd config and ensure that all needed is enabled
``` ansible-playbook systemd.yml -K -u [user] ```
To sum up, I think, that task wich was given to me was done only nearly to 60-70 percents. Why it's done so? More discriptions of steps and fails are in Main manual.
How I see perfect solution - part of bash scripts wich start ansible playbooks and in the end simple curl test.
