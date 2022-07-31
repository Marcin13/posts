---
author: "Marcin M"
title: "Generate an SSH Key for Ansible"
date: 2022-07-14T13:36:08+01:00
description: "Generate an ssh key that’s going to be specifically used for Ansible."
aliases: ["Ansible", "SSH"]
draft: true
categories: ["Ansible", "SSH"]
tags: ["Ansible", "SSH"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
   URL: "https://github.com/Marcin13/posts/blob/master/Generate_an_SSH_Key_for_Ansible.md"
---
Generate an ssh key 'strong key'

```shell
ssh-keygen -t ed25519 -C "ansible"
```

Then chose folder and name for ssh key
```shell
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/marcin/.ssh/id_ed25519): /home/marcin/.ssh/ansible/ansible
```

Copy the ssh key to the server(s)
> but first you have to log in by name and password
```shell
ssh-copy-id -i ~/.ssh/ansible.pub <IP Adderss>
```

Use an SSH key to connect to a server
```shell
ssh -i .ssh/<key_name> <IP Address>
```

To cache the passphrase for our session, we can use the ssh-agent
```shell
eval $(ssh-agent) &&b ssh-add
```

Here’s an alias you can put in your .bashrc, to simplify it
```shell
alias ssha='eval $(ssh-agent) && ssh-add'
```
### useful links:
[https://www.ssh.com/academy/ssh/keygen](https://www.ssh.com/academy/ssh/keygen)
