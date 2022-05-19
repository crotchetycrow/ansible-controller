# Creating an EC2 instance using Ansible playbooks

## Create public key for private key

`cd ~/.ssh`

`sudo key-gen -y` (Path to private key)

Copy generated key

`sudo nano file.pub` - Paste generated key (file name must match private key)

## Playbook for creating an EC2 instance

`sudo nano playbook.yml`

Information needed to create an EC2 instance through Ansible:

- AMI ID
- Security Group ID
- Key name
- EC2 name
- EC2 tag name

```
---
- hosts: localhost
  connection: local
  gather_facts: yes

  vars:
    key_name: insert key_name
    region: eu-west-1
    image: insert AMI here
    id: "insert name here"
    sec_group: "insert security group here"

  tasks:
    - name: Facts
      block:
        - name: Get instances facts
          ec2_instance_info:
            aws_access_key: "{{ec2_access_key}}"
            aws_secret_key: "{{ec2_secret_key}}"
            region: "{{ region }}"
          register: result

    - name: Provisioning EC2 instances
      block:
        - name: Create security group
          ec2_group:
            name: "{{ sec_group }}"
            description: "eng110_jack_ansible_app"
            # vpc_id: 12345
            region: "{{ region }}"
            aws_access_key: "{{ec2_access_key}}"
            aws_secret_key: "{{ec2_secret_key}}"
          register: result_sec_group

        - name: Upload public key to AWS
          ec2_key:
            name: "{{ key_name }}"
            key_material: "{{ lookup('file', '~/.ssh/{{ key_name }}.pub') }}"
            region: "{{ region }}"
            aws_access_key: "{{ec2_access_key}}"
            aws_secret_key: "{{ec2_secret_key}}"

        - name: Provision instance(s)
          ec2:
            aws_access_key: "{{ec2_access_key}}"
            aws_secret_key: "{{ec2_secret_key}}"
            id: "{{ id }}"
            key_name: "{{ key_name }}"
            group_id: "sg-{{ sec_group }}"
            image: "{{ image }}"
            instance_type: t2.micro
            region: "{{ region }}"
            wait: true
            count: 1
            # exact_count: 2
            # count_tag:
            #   Name: App
            instance_tags:
              Name: insert tag name here

      tags: ["never", "create_ec2"]
```

`sudo ansible-playbook playbook.yml --ask-vault-pass` - To run and check functionality

`ansible-playbook playbook.yml --ask-vault-pass --tags create_ec2` - To execute task

## Playbook for EC2 setup with NGINX, nodejs, npm

`sudo nano playbook.yml`

```
---
- hosts: aws
  gather_facts: yes
  become: true

  tasks:
    - name: Install nginx
      apt: pkg=nginx state=present
    - name: Install software-properties-common
      apt: pkg=software-properties-common state=present
    - name: add nodejs apt key
      apt_key:
        url: https://deb.nodesource.com/gpgkey/nodesource.gpg.key
        state: present
    - name: install nodejs
      apt_repository:
        repo: deb https://deb.nodesource.com/node_13.x bionic main
        update_cache: yes
    - name: install nodejs
      apt:
        update_cache: yes
        name: nodejs
        state: present
    - name: start npm
      shell: |
        cd app/app/
        npm install
        npm start

```

`sudo ansible-playbook playbook.yml --ask-vault-pass`

Once app EC2 has been created, create a playbook to spin up a db instance. This is a similar format but you will need a different AMI id and Security Group.

Then, install MongoDB.

## Playbook for EC2 db setup

`sudo nano playbook.yml`

```
---
- hosts: db
  gather_facts: yes
  become: true

  tasks:
    - name: apt-key for mongodb
      apt_key:
        url: hkp://keyserver.ubuntu.com:80
        id: 9DA31620334BD75D9DCB49F368818C72E52529D4
        state: present
    - name: add mongodb version
      apt_repository:
        repo: deb [arch=amd64] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse
        update_cache: yes
    - name: install mongodb
      apt: pkg=mongodb-org state=present
    - name: start and enable mongod
      shell: |
        sudo systemctl start mongod
        sudo systemctl enable mongod
```

`sudo ansible-playbook playbook.yml --ask-vault-pass`

Once MongoDB is installed, you need to set the DB_HOST environment variable on the EC2 app instance.

## Playbook for setting the DB_HOST environment variable

`sudo nano playbook.yml`

```
---
- hosts: aws
  gather_facts: yes
  become: true

  tasks:
    - name: set environment variable
      shell: |
        export DB_HOST=mongodb://db-ip:27017/posts
```

`sudo ansible-playbook playbook.yml --ask-vault-pass`

## Playbook for NPM install and start

`sudo nano playbook.yml`

```
---
- hosts: web
  gather_facts: yes
  become: yes
  tasks:
    - name: change dir
      shell: |
        cd ./app/app/
        sudo apt-get update -y
        npm install
        npm start
```

`sudo ansible-playbook playbook.yml --ask-vault-pass`
