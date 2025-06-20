---
author: "Marcin M"
title: "WireGuard Installation"
date: 2025-06-20T21:02:38+01:00
description: "Step-by-step guide to installing and configuring WireGuard VPN on a clean Ubuntu server"
aliases: []
draft: false
categories: ["Networking", "VPN", "Linux"]
tags: ["WireGuard", "Ubuntu", "VPN", "Security", "Networking"]
ShowToc: true
TocOpen: true
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Wireguard-Installation.md"
---

# WireGuard Installation on a Clean Ubuntu Server

This guide walks you through installing and configuring **WireGuard VPN** on a fresh **Ubuntu server**, step by step. It’s perfect for users hosting a VPS on providers like OVH, Hetzner, or DigitalOcean.

---

## ✅ Step 1: Update the Server and Install Packages

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install wireguard iptables iptables-persistent net-tools -y
```

## ✅ Step 2: Enable IP Forwarding

To allow the server to forward packets between interfaces, you need to enable IP forwarding. This is crucial for routing traffic through the VPN.

```bash
echo 'net.ipv4.ip_forward=1' | sudo tee /etc/sysctl.d/99-wireguard.conf
sudo sysctl --system
```

verify that IP forwarding is enabled:

```bash
sysctl net.ipv4.ip_forward
```

expected output:

```sh
net.ipv4.ip_forward = 1
```

## ✅ Step 3: Generate WireGuard Keys

```bash
cd /etc/wireguard
sudo umask 077
wg genkey | tee server_private.key | wg pubkey > server_public.key
```

## ✅ Step 4: Configure WireGuard Server

Create the WireGuard configuration file for the server:

```bash
sudo nano /etc/wireguard/wg0.conf
```

Add the following configuration, replacing `YOUR_SERVER_PUBLIC_KEY` and `YOUR_CLIENT_PUBLIC_KEY` with the actual keys generated in the previous step:

```ini
# ============================
# WireGuard Server Configuration (wg0)
# ============================

[Interface]
# This is the internal VPN IP address of the server.
# It's part of a private subnet (10.0.0.0/24). The server uses 10.0.0.1.
Address = 10.0.0.1/24

# This is the UDP port the server listens on.
# Clients will connect to this port.
ListenPort = 51820

# The server's private key.
# Generate with: wg genkey
# NEVER share this key — it's private!
# The format of the PublicKey = pr1vatekey1234567890abcde
PrivateKey = <server_private_key>

# ============================
# NAT + Routing Rules (PostUp)
# ============================

# Enable NAT and routing when the interface comes up.
# Must be a single line — no backslashes!
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE
# This tells WireGuard to overwrite this config file with any live changes made via `wg` command.
# If you modify peers or settings on-the-fly, they'll be saved here on shutdown.
SaveConfig = true

# ============================
# First Peer (Client Device)
# ============================

[Peer]
# The public key of the client.
# This is how the server verifies the client's identity.
# The client generated it from their private key using: wg pubkey
# The format of the PublicKey = 6ubkey1234567890abcde
PublicKey = <client_public_key>
# The IP address this client will use inside the VPN.
# /32 is a CIDR mask meaning: ONLY 10.0.0.2 — no range.
# This ensures the server sends traffic meant only for 10.0.0.2 to this peer.
# ┌────────────┬──────────────────────────────────┐
# │ CIDR Mask  │ Meaning                          │
# ├────────────┼──────────────────────────────────┤
# │ /32        │ Single IP (e.g. 10.0.0.2)        │
# │ /24        │ 256 IPs (10.0.0.0–10.0.0.255)    │
# │ /0         │ All IPs (0.0.0.0–255.255.255.255)│
# └────────────┴──────────────────────────────────┘
AllowedIPs = 10.0.0.2/32

```

Replace `<server_private_key>` with the content of `server_private.key` and `<client_public_key>` with the content of `client_public.key`.

Make sure to replace `ens3` with your actual network interface name if it's different. You can find your interface name using the command:

```bash
ip route get 1.1.1.1
```

## ✅ Step 5: Start and Enable WireGuard

```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
sudo systemctl status wg-quick@wg0
```

## ✅ Step 6: Configure the Client

Windows Client Configuration Example

```bash
[Interface]
PrivateKey = <client_private_key>
Address = 10.0.0.2/24
DNS = 1.1.1.1

[Peer]
PublicKey = <server_public_key>
Endpoint = <your-server-ip>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

Replace `<client_private_key>` with the content of `client_private.key`, `<server_public_key>` with the content of `server_public.key`, and `<your-server-ip>` with your server's public IP address.

For Linux clients, you can use the same configuration format in a file named `wg0.conf` and place it in `/etc/wireguard/`.

## ✅ Step 7: Start the Client

For Linux clients, you can start the WireGuard interface with:

```bash
sudo wg-quick up wg0
```

For Windows clients, use the WireGuard application to import the configuration file and activate the tunnel.

## ✅ Step 8: Verify the Connection

On the server, check the status of the WireGuard interface:

```bash
sudo wg show
```

You should see the client connected with its public key and allowed IPs.

## ✅ Step 9: Test the VPN Connection

On the client, you can test the VPN connection by pinging the server's WireGuard IP:

```bash
ping <server_ip>
```

If you receive replies, the VPN is working correctly.

## ✅ Step 10: Configure Firewall Rules (Optional)

If you have a firewall enabled (like UFW), you need to allow the WireGuard port (51820) and enable forwarding:

```bash
sudo ufw allow 51820/udp
sudo ufw enable 
sudo ufw status
```

## ✅ Step 11: Save iptables Rules (Optional)

If you want to make sure your iptables rules persist after a reboot, you can save them using `iptables-persistent`.
Make sure `ens3` is your actual network interface name. You can find your interface name using the command: ` ip route get`.
The following commands should be already executed in the `/etc/wireguard/wg0.conf` file under `PostUp` and `PostDown` sections, but you can also run them manually to ensure they are applied:

```bash
sudo iptables -A FORWARD -i wg0 -j ACCEPT
sudo iptables -A FORWARD -o wg0 -j ACCEPT
sudo iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
sudo netfilter-persistent save
```

## ✅ Step 12: Reboot and Test

Finally, reboot your server to ensure everything starts correctly:

```bash
sudo reboot
```

After the reboot, check the status of the WireGuard service again:

```bash
sudo systemctl status wg-quick@wg0
```

You should see that the service is active and running.

## Conclusion

Congratulations! You have successfully installed and configured WireGuard VPN on your Ubuntu server. You can now securely connect to your server from your client devices.
From my experience, the ` ipv6 ` not to show up when you check my IP under the running WireGuard. On the websites like `https://ipleak.net/` or `https://www.iplocation.net/`.


## Additional Resources

For more information on WireGuard, you can refer to the official documentation:
[WireGuard Documentation](https://www.wireguard.com/)

## Troubleshooting

If you encounter any issues, check the following:

- Ensure that the WireGuard service is running on both the server and client.
- Verify that the IP addresses and keys in the configuration files are correct.
- Check the firewall settings to ensure that the WireGuard port is open.
- Look at the logs for any error messages:

```bash
sudo journalctl -u wg-quick@wg0

```

## Additional Notes

- Make sure to keep your private keys secure and never share them.
- Consider using a dynamic DNS service if your server's IP address changes frequently.
- For enhanced security, consider setting up a firewall and fail2ban to protect against unauthorized access attempts.

## Troubleshooting Tips

If you encounter issues with the WireGuard connection, here are some troubleshooting tips:

## Thanks for reading

If you found this guide helpful, please consider sharing it with others who might benefit from it. Your feedback is also appreciated, so feel free to leave comments or suggestions.
If you have any questions or need further assistance, feel free to reach out. Happy tunneling! 🚀

