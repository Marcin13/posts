---
author: "Marcin M"
title: "Ansible setup"
date: 2022-09-24T07:04:49+01:00
description: "Installing and using Ansible with Python 3 and PIP"
aliases: ["Ansible","setup"]
draft: true
categories: ["Ansible","up and run"]
tags: ["Ansible","setup"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
    URL: "https://github.com/Marcin13/posts/blob/master/Ansible_setup.md"
---
This guide explains how to install Ansible using Python 3 and PIP. It covers checking for Python 3, installing Ansible with PIP, and providing shell scripts for automation. It also includes an introduction to Ansible and a brief conclusion.

[comment]:
[//]: #

We can check which version of Python3 is installed on our system by running the command:

```shell
which python3
```

To check the version of Ansible installed on our system, we can run:

```shell
ansible --version
```

If Ansible is not installed, we can follow these steps to install the latest version using PIP, the Python package manager:

```shell
#!/bin/bash
python3 -m pip install --upgrade --user pip
python3 -m pip install --user ansible
```

We can also install Ansible globally by running the following commands:

```shell
#!/bin/bash
python3 -m pip install --upgrade pip
python3 -m pip install ansible
```

### Conclusion

In conclusion, in this tutorial, we have learned how to check the versions of Python3 and Ansible installed on our system,
as well as how to install the latest version of Ansible using PIP. Ansible is a powerful tool for automating IT tasks,
and understanding how to install and configure it is an essential skill for any systems administrator or DevOps engineer.

### Useful links
