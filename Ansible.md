---
author: "Marcin M"
title: "Ansible"
date: 2022-07-14T17:14:30+01:00
description: "Ansible comments and initial configuration."
aliases: ["Ansible"]
draft: true
categories: ["Ansible"]
tags: ["Ansible", "automation"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
    URL: "https://github.com/Marcin13/posts/blob/master/Ansible.md"
---
Please go to official web to get instruction how to install Ansible - this is the best way.
[Installing and upgrading Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible)

Ansible's installation and version
```shell
ansible --version
```

Create Inventory
```shell
#inventory file
192.168.1.171
192.168.1.172
192.168.1.173
```

Create ansible.cfg
```shell
#ansible.cfg
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible/
remote_user = ubuntu
```

Test Ansible is working
 ```shell
ansible all --key-file ~/.ssh/ansible -i inventory -m ping
```

After create config.cfg ansible command can be simplified
```shell
ansible all -m ping
```

List all the hosts in the inventory
```shell
ansible all --list-hosts
```

Gather facts about your hosts
```shell
ansible all -m gather_facts
```

Gather facts about your hosts, but limit it to just one host
```shell
ansible all -m gather_facts --limit 192.168.1.171
```
## install apache and php support for apache
install_apache.yml
```yaml
---

- hosts: all
  become: true
  tasks:

  - name: install apache and php for Ubuntu servers
    apt:
      name:
        - apache2
        - libapache2-mod-php
      state: latest
      update_cache: yes
    when: ansible_distribution == "Ubuntu"
```

## remove apache package and remove php support for apache
remove_apache.yml
```yaml
---
 
- hosts: all
  become: true
  tasks:
 
  - name: remove apache2 package
    apt:
      name: apache2
      state: absent
 
  - name: remove php support for apache
    apt:
      name: libapache2-mod-php
      state: absent
```
with parametr -C, --check don't make any changes; instead, try to predict some of the changes that may occur
```shell
ansible-playbook -C remove_apache.yml 
```
Ansible documentation 
```shell
ansible-doc service
```

