---
author: "Marcin M"
title: "SSL Certificate for Use With Nginx on Localhost"
date: 2023-03-27T17:41:32+01:00
description: "Yes, you can generate a TLS/SSL certificate for use with Nginx on localhost. "
aliases: ["Nginx", "server"]
draft: true
categories: ["server"]
tags: ["Nginx"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/SSL-certificate-for-use-with-Nginx-on-localhost.md"
---
## Here are the general steps you can follow:
1. Install OpenSSL on your local machine, if it is not already installed. OpenSSL is a command-line tool used to generate the certificate.
2. Generate a self-signed certificate using the following command:

```shell
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout localhost.key -out localhost.crt

```
This command will generate a private key **(localhost.key)** and a self-signed certificate **(localhost.crt)** that is valid for
365 days. During the process, you'll be prompted to enter some information about the certificate, such as the Common Name (CN),
which should be set **to localhost** in this case.

3. Move the **localhost.key** and **localhost.crt** files to a directory that Nginx can access. For example, you can create a new 
directory called ssl in your Nginx configuration directory and move the files there:

```shell
sudo mkdir /etc/nginx/ssl
sudo mv localhost.key localhost.crt /etc/nginx/ssl/

```
4. Update your Nginx configuration to use the SSL certificate. Here's an example server block:
```nginx configuration
server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate /etc/nginx/ssl/localhost.crt;
    ssl_certificate_key /etc/nginx/ssl/localhost.key;

    location / {
        root /var/www/html;
        index index.html;
    }
}

```
This server block configures Nginx to listen on **port 443 (HTTPS)**, use the SSL certificate, and serve files from the **/var/www/html** directory.
Replace this with your own configuration as needed.
5. Restart Nginx to apply the changes:
```shell
sudo systemctl restart nginx

```
You should now be able to access your local Nginx server over HTTPS by visiting [https://localhost](https://localhost) in your web browser.
Note that since this is a self-signed certificate, your browser will likely show a warning about the site's security. 
You can safely ignore this warning since you generated the certificate yourself.

[//]: # (![Screenshot.png]&#40;http://marcinmitruk.link/img/SSL-Certificate-for-Use-With-Nginx-on-Localhost/Screenshot1.png&#41;)






### Useful links:

