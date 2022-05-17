Install Ansible
`sudo apt-get install software-properties-common -y`
`sudo apt-add-repository ppa:ansible/ansible`
`sudo apt-get update -y`
`sudo apt-get install ansible -y`
`ansible --version`

Check connection
`ssh vagrant@192.168.56.10` - for web
Password = 'vagrant'
`ssh vagrant@192.168.56.11` - for db
Password = 'vagrant'
`sudo apt-get update -y`

Connecting web via hosts
`cd /etc/ansible/`
`sudo apt-get install tree -y` - not required for Ansible, helps visualise file structure
`sudo nano hosts`

```
[web]
192.168.56.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
[db]
192.168.56.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
```

Test connectivity
`ansible web -m ping`
`ansible db -m ping`
