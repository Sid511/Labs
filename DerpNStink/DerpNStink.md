# DerpNStink: 1 VulnHub Machine Walkthrough (PL)
## Wstęp
W poniższym walkthrough zajmiemy się podatną VM dostępną na https://www.vulnhub.com. To ciekawe wyzwanie boot2root, będzie od nas wymagało
podejścia i działania "out-of-the-box", ponieważ zahacza o wiele różnych obszarów i tooli dla pentesterów.
Uprzedzam, że zadanie zawiera kilka zagwozdek, jednak wspólnie stawimy im dzisiaj czoła i mam nadzieję będzie to dla nas wszystkich równie satysfakcjonujące! Zachęcam również do samodzielnego, dalszego eksplorowania
wszystkich omawianych zagadnień. **Prezentowany sposób nie jest wyłącznym możliwym, a jedynie jednym z wielu.** Eksperymentowanie i wypracowanie sobie własnego sposobu hackowania jest tutaj mile widziane.
Autor zostawił dla nas następującą wiadomość:


>_Your goal is to remotely attack the VM and find all 4 flags eventually leading you to full root access. Don't forget to #tryharder_

>_Example: flag1(AB0BFD73DAAEC7912DCDCA1BA0BA3D05). Do not waste time decrypting the hash in the flag as it has no value in the challenge other than an identifier_.

Zamiast marnować czas, **let's cut straight to the chase**! :-)

## 1. Inicjalny skan & rekonesans

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
Nmap odkrył przed nami dostępne i działające usługi **FTP, SSH i HTTP**
Szczególnie interesująca wydaje się ostatnia z nich. Upewnijmy się co do **portu nr 80**



## 2. 
