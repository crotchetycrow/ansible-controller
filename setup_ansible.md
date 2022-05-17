## Install Ansible

`sudo apt-get install software-properties-common -y`
`sudo apt-add-repository ppa:ansible/ansible`
`sudo apt-get update -y`
`sudo apt-get install ansible -y`
`ansible --version`

## Check connection

`ssh vagrant@192.168.56.10` - for web
Password = 'vagrant'
`ssh vagrant@192.168.56.11` - for db
Password = 'vagrant'
`sudo apt-get update -y`

## Connecting web via hosts

`cd /etc/ansible/`
`sudo apt-get install tree -y` - not required for Ansible, helps visualise file structure
`sudo nano hosts`

```
[web]
192.168.56.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
[db]
192.168.56.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
```

## Test connectivity

`ansible web -m ping`
`ansible db -m ping`

## Adhoc commands in Ansible (Examples of agentlessness)

### Checking OS

`ansible all -a "uname -a"` - Names OS of all hosts

### Installing tree

`ansible web -a "sudo apt-get install tree -y"`

### Copying files

`ansible web -m copy -a 'src=/etc/ansible/test.txt dest=/home/vagrant'`

### Space available on server

`ansible web -a "free"`

### Check status of nginx

`ansible web -a "systemctl nginx"`

## Setting up a playbook for nginx

```
# creating a playbook to confiure Nginx web server in web machine
# YAML YML file starts with three dashs ---
---

# Where do we want to install nginx
- hosts: web
# Do we want to see logs - gather facts
  gather_facts: yes
# What permissions do we need to run this playbook
  become: true
# used for sudo/admin
# what is the name of the package
  tasks:
  - name: install Nginx
    apt: pkg=nginx state=present
# state=inactive
# What should be the status of nginx once the playbook is finish
# Command to run Playbook - ansible-playbook file.yml
```

### Playbook for setting up app

```
- hosts: web
  gather_facts: yes
  become: true
  tasks:
  - name: node app set up
    shell: |
      cd app/app/
      sudo apt-get install software-properties-common -y
      curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
      sudo apt-get install -y nodejs
      sudo apt-get update -y
      npm install
      npm start
```
