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

Próba zakończona powodzeniem. W rezultacie trafiamy do panelu wordpressa jako użytkownik z ograniczonymi uprawnieniami.
![](https://github.com/d15rup7or/Labs/blob/master/DerpNStink/img/slideshow.png)




## 2. 

