---
author: "Marcin M"
title: "Automating Ubuntu Server Restarts: Never Wait for Manual Commands Again"
date: 2023-07-06T18:32:43+01:00
description: "Learn how to automate the process of restarting your Ubuntu server, eliminating the need for manual commands. Keep your server running smoothly and optimized without any downtime or manual intervention."
aliases: ["ubuntu", "server"]
draft: true
categories: ["ubuntu"]
tags: ["restart"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Restart-ubuntu-server.md"
---
There comes a time when your server requires regular restarts, and it can be a hassle to do it manually every time. However, there is a way to automate this process and make your life easier. Enter Systemd, a powerful init system that provides a convenient solution.

With Systemd, you can create a service unit that defines the desired behavior for your server restarts. By configuring a few settings, you can schedule automatic restarts at specific intervals, ensuring your server stays fresh and optimized without any manual intervention.

To set it up, you'll utilize Systemd's timer functionality. By creating a timer unit and linking it to your service unit, you can define the schedule for the server restarts. Whether it's daily, weekly, or any other interval, Systemd has you covered.

Once everything is configured, Systemd takes care of the rest. It will handle the automatic restarts according to your defined schedule, allowing you to focus on more important tasks without worrying about server maintenance.

So, say goodbye to manual restarts and embrace the automation provided by Systemd. Let me show you how to set it up and enjoy the benefits of a smoothly running server."

I hope this captures the essence of your message in a more professional and engaging manner

Lets start.

##### 1. Create a new systemd timer unit file using the following command:

```shell
sudo nano /etc/systemd/system/reboot.timer
```

#####  2. In the editor, add the following lines:

```text
[Unit]
Description=Reboot System

[Timer]
OnCalendar=*-*-* 06:00:00
Persistent=true

[Install]
WantedBy=timers.target

```

##### 3. Save the file and exit the editor.
##### 4. Create the reboot.service unit file by running the following command:

```bash
sudo nano /etc/systemd/system/reboot.service
```

##### 5. Add the following content to the file:

```text
[Unit]
Description=Reboot Service

[Service]
Type=oneshot
ExecStart=/sbin/reboot

[Install]
WantedBy=default.target

```

##### 6. Save the file and exit the editor.

##### 7. Enable the timer unit by running the following command:

```bash
sudo systemctl daemon-reload
sudo systemctl enable reboot.timer
sudo systemctl start reboot.timer
sudo systemctl status reboot.timer
sudo systemctl status reboot.service

```
##### 8. Summary:
```yaml
user@server:~$ sudo systemctl status reboot.service
● reboot.service - Reboot Service
     Loaded: loaded (/etc/systemd/system/reboot.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
TriggeredBy: ● reboot.timer
user@server:~$ sudo systemctl status reboot.timer
● reboot.timer - Reboot System
     Loaded: loaded (/etc/systemd/system/reboot.timer; enabled; vendor preset: enabled)
     Active: active (waiting) since Thu 2023-07-06 17:54:11 UTC; 1min 42s ago
    Trigger: Fri 2023-07-07 06:00:00 UTC; 12h left
   Triggers: ● reboot.service

Jul 06 17:54:11 larablogger.com systemd[1]: Started Reboot System.
user@server:~$ 
```

The reboot.timer is enabled, and it is currently active and waiting for the trigger time to initiate the reboot.
The reboot.service is loaded and inactive because it is triggered by the reboot.timer and will be activated at the specified trigger time.
In your case, the reboot.timer is set to trigger the reboot.service on the next occurrence of Fri 2023-07-07 06:00:00 UTC, which is 12 hours away from the current time.

Therefore, based on the status output, everything seems to be working correctly. The server will automatically reboot at the specified trigger time, and the reboot.service will be activated by the reboot.timer.

You can check the status again closer to the trigger time to see if the reboot.service becomes active and if the server restarts as expected.

![Screenshot.png](http://marcinmitruk.link/img/Restart-Ubuntu-Server/Screenshot1.png)

### Useful links:
