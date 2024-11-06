---
title: 'GOAD - Installation for Windows'
layout: mypost
categories: [GOAD]
---


![goad](/assets/img/goad/goad.png)

> GOAD is a pentest active directory LAB project. The purpose of this lab is to give pentesters a vulnerable Active directory environment ready to use to practice usual attack techniques. - https://github.com/Orange-Cyberdefense/GOAD 
{: .prompt-info }

<h3>The installation doesn't cover Windows as host, so I made a step by step tutorial.</h3>

#### Made a Medium blog containing all the steps here: 
```
https://medium.com/p/5d987f0228bd
```
PS: v2 version only as v3 is in beta and has bugs.

#### List of some possible attacks contained in the lab:

```
Password reuse between computer (PTH)
Spray User = Password
Password in description
SMB share anonymous
SMB not signed
Responder
Zerologon
Windows defender
ASREPRoast
Kerberoasting
AD Acl abuse
Unconstraint delegation
Ntlm relay
Constrained delegation
Install MSSQL
MSSQL trusted link
MSSQL impersonate
Install IIS
Upload asp app
Multiples forest
Anonymous RPC user listing
Child parent domain
Generate certificate and enable ldaps
ADCS - ESC 1/2/3/4/6/8
Certifry
Samaccountname/nopac
Petitpotam unauthent
Printerbug
Drop the mic
Shadow credentials
Mitm6
Add LAPS
GPO abuse
Add Webdav
Add RDP bot
Add full proxmox integration
Add Gmsa (receipe created)
Add azure support
Refactoring lab and providers
Protected Users
Account is sensitive
Add PPL
Add Gmsa
Groups inside groups
Shares with secrets (all, sysvol)
```