# Ansible

- [Setting up Ansible](https://github.com/crotchetycrow/ansible-controller/blob/main/documentation/setup_ansible.md)
- [Ansible vault setup](https://github.com/crotchetycrow/ansible-controller/blob/main/documentation/ansible_vault.md)
- [Setting up an EC2 with Ansible](https://github.com/crotchetycrow/ansible-controller/blob/main/documentation/ansible_ec2_setup.md)
- [Infrastructure as Code](https://github.com/crotchetycrow/ansible-controller/blob/main/documentation/iac.md)

## What is Ansible?

Ansible is an open sourceIT automation tool that automates provisioning, configuration management, application deployment, orchestration, and many other manual IT processes. Unlike more simplistic management tools, Ansible users (like system administrators, developers and architects) can use Ansible automation to install software, automate daily tasks, provision infrastructure, improve security and compliance, patch systems, and share automation across the entire organization.

Ansible works by connecting to what you want automated and pushing programs that execute instructions that would have been done manually.

## Why is Ansible agentless?

Ansible is agentless, meaning that it does not install software on the nodes that it manages. This removes a potential point of failure and security vulnerability and simultaneously saves system resources.

It uses SSH protocol to connect the servers. Ansible requires Python to make the use of modules on client machines.

## What are roles in Ansible?

Roles provide a framework for fully independent, or interdependent collections of variables, tasks, files, templates, and modules.

In Ansible, the role is the primary mechanism for breaking a playbook into multiple files. This simplifies writing complex playbooks, and it makes them easier to reuse.

Each role is basically limited to a particular functionality or desired output, with all the necessary steps to provide that result either within that role itself or in other roles listed as dependencies.

Roles are not playbooks. Roles are small functionality which can be independently used but have to be used within playbooks. There is no way to directly execute a role. Roles have no explicit setting for which host the role will apply to.

Top-level playbooks are the bridge holding the hosts from your inventory file to roles that should be applied to those hosts.

## How is Ansible different from simply running a provisioning bash sell?

Bash (or another scripting language) describes actions. Do this thing. Now do this other thing.

Ansible (and other configuration management systems) describe the desired state. This file should exist here, owned by this user, with these permissions. This package should be installed. This line in this config file should appear like this.
