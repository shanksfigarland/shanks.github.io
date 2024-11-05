---
title: 'All about Enumeration: OSCP version'
categories: [OSCP] 
layout: mypost
---

> IN PROGRESS 
{: .prompt-warning }

## <span style="color: #74C7D2">Enumeration is key</span>

As a OSCP holder, here's how I would enumerate the machines. 


### Rustscan (all ports)

I'd do a quick [rustscan](https://github.com/RustScan/RustScan) for all ports first and pipe the ports into nmap -p

Example:
```bash
rustscan 192.168.26.14

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
🌍HACK THE PLANET🌍

[~] The config file is expected to be at "/home/rustscan/.rustscan.toml"
[~] File limit higher than batch size. Can increase speed by increasing batch size '-b 1048476'.
Open 192.168.26.14:22
Open 192.168.26.14:80
Open 192.168.26.14:81
Open 192.168.26.14:135
```

### Nmap (all ports with service scan)

Pipe the ports into nmap.

```bash
nmap 192.168.26.14 -sV -sC -vv -p22,80,81,135 -T4 -Pn -oN allports

# Nmap 7.94 scan initiated Tue Jan 15 12:24:57 2024 as: nmap -sV -sC -vv -T4 -p22,80,81,135 -oN service 192.168.26.14
Nmap scan report for 192.168.26.14
Host is up, received syn-ack (0.13s latency).
Scanned at 2024-01-15 12:24:57 -03 for 179s

PORT      STATE SERVICE       REASON  VERSION
22/tcp    open  ssh           syn-ack OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 e0:3a:63:4a:07:83:4d:0b:6f:4e:8a:4d:79:3d:6e:4c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAgLLakGf2MPORvtZSeF1gAL03sfUo/E/
|   256 3f:16:ca:3 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlNV8ECDl5yUqV7a41c39cXyPE6
|   256 fe:b0:7a:14 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICyZUVd
80/tcp    open  http          syn-ack Apache httpd 2.4.51 
81/tcp    open  http          syn-ack Apache httpd 2.4.51 
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
```