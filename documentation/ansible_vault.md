`sudo apt update -y`

`sudo apt-get install tree -y `

`sudo apt-add-repository --yes --update ppa:ansible/ansible`

`sudo apt-get install ansible -y`

`alias python=python3`

`sudo apt-get install python3-pip`

`pip3 install awscli ` OR `sudo python -m pip install awscli`

`pip3 install boto boto3` OR `sudo python -m pip install boto boto3`

`sudo apt-get update -y`

`sudo apt-get upgrade -y`

- Set up ansible vault - AWS access & secret keys
- Create folder structure for ansible vault
- Transfer/cp file.pem to ansible controller
- Generate ssh key on the controller to use as a ssh key pair between controller and agent nodes
- Check to see if the keys have been secured with ansible-vault

ansible/

`mkdir group_vars`
`cd group_vars`
`mkdir all`
`cd all`
`sudo ansible-vault create pass.yml` - `sudo ansible-vault edit pass.yml` (to edit)
`ec2_access_key: `
`ec2_secret_key: `
`cd ~/.ssh`
`sudo nano eng119.pem`
Copy from local to controller
`sudo chmod 400 eng119.pem`
[aws]
ec2-instance ansible_host=aws-public-ip ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/eng119.pem

`ansible-playbook 'playbook' --ask-vault-pass`
