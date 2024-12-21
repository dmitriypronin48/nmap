# Домашнее задание к занятию «Уязвимости и атаки на информационные системы» - `Пронин Дмитрий Андреевич`

---



### Задание 1

Скачайте и установите виртуальную машину Metasploitable: https://sourceforge.net/projects/metasploitable/.

Это типовая ОС для экспериментов в области информационной безопасности, с которой следует начать при анализе уязвимостей.

Просканируйте эту виртуальную машину, используя nmap.

Попробуйте найти уязвимости, которым подвержена эта виртуальная машина.

Сами уязвимости можно поискать на сайте https://www.exploit-db.com/.

Для этого нужно в поиске ввести название сетевой службы, обнаруженной на атакуемой машине, и выбрать подходящие по версии уязвимости.

Ответьте на следующие вопросы:

* Какие сетевые службы в ней разрешены?
```
Starting Nmap 7.80 ( https://nmap.org ) at 2024-12-20 20:18 MSK
Nmap scan report for 192.168.0.200
Host is up (0.00028s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login?
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
MAC Address: 08:00:27:4E:BD:0A (Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.72 seconds

```

* Какие уязвимости были вами обнаружены? (список со ссылками: достаточно трёх уязвимостей)
Приведите ответ в свободной форме.
уязвимость vsftpd 2.3.4 https://www.exploit-db.com/exploits/49757 
уязвимость сервера DNS https://www.exploit-db.com/exploits/6122
уязвимость сервера postgres https://www.exploit-db.com/exploits/7855
---


### Задание 2
Проведите сканирование Metasploitable в режимах SYN, FIN, Xmas, UDP.

Запишите сеансы сканирования в Wireshark.

Ответьте на следующие вопросы:

* Чем отличаются эти режимы сканирования с точки зрения сетевого трафика?
* Как отвечает сервер?

SYN Успешные SYN-паки -> SYN-ACK (открытые порты), Закрытые порты -> RST
При отправке SYN-пакетов на порты целевого хоста проверяется, открыт ли порт. Если порт открыт, сервер ответит пакетом SYN-ACK. Если закрыт, ответ будет RST.


FIN FIN -> нет ответа (открытый порт), FIN -> RST (закрытый порт)
TCP-стеки должны игнорировать FIN-пакеты для закрытых портов и не отправлять никаких ответов, а открытые порты отвечают RST

Xmas -> нет ответа (открытый порт), Xmas -> RST (закрытый порт)
Установку флагов FIN, URG и PSH одновременно. Похож на fin сканирование, открытые порты игнорируют эти пакеты, в то время как закрытые порты отправляют RST.

UDP пакет -> ICMP “Port Unreachable” (закрытый порт), UDP пакет -> ответ (открытый порт), UDP пакет -> нет ответа (открытый или фильтруемый порт)
UDP-сканирование отправляет пустые UDP-пакеты на заданные порты, если порт открыт, может быть получен ответ. Если закрыт, сервер может вернуть ICMP-пакет “Port Unreachable”.


Сканирование
```
root@k3s-server:~# nmap -sS 192.168.0.199
Starting Nmap 7.80 ( https://nmap.org ) at 2024-12-21 13:46 MSK
Nmap scan report for 192.168.0.199
Host is up (0.00036s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 08:00:27:4E:BD:0A (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
root@k3s-server:~# nmap -sS 192.168.0.199
Starting Nmap 7.80 ( https://nmap.org ) at 2024-12-21 13:56 MSK
Nmap scan report for 192.168.0.199
Host is up (0.00025s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 08:00:27:4E:BD:0A (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
root@k3s-server:~# nmap -sF 192.168.0.199
Starting Nmap 7.80 ( https://nmap.org ) at 2024-12-21 13:57 MSK
Nmap scan report for 192.168.0.199
Host is up (0.00031s latency).
Not shown: 977 closed ports
PORT     STATE         SERVICE
21/tcp   open|filtered ftp
22/tcp   open|filtered ssh
23/tcp   open|filtered telnet
25/tcp   open|filtered smtp
53/tcp   open|filtered domain
80/tcp   open|filtered http
111/tcp  open|filtered rpcbind
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
512/tcp  open|filtered exec
513/tcp  open|filtered login
514/tcp  open|filtered shell
1099/tcp open|filtered rmiregistry
1524/tcp open|filtered ingreslock
2049/tcp open|filtered nfs
2121/tcp open|filtered ccproxy-ftp
3306/tcp open|filtered mysql
5432/tcp open|filtered postgresql
5900/tcp open|filtered vnc
6000/tcp open|filtered X11
6667/tcp open|filtered irc
8009/tcp open|filtered ajp13
8180/tcp open|filtered unknown
MAC Address: 08:00:27:4E:BD:0A (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.55 seconds
root@k3s-server:~# nmap -sX 192.168.0.199
Starting Nmap 7.80 ( https://nmap.org ) at 2024-12-21 13:57 MSK
Nmap scan report for 192.168.0.199
Host is up (0.00024s latency).
Not shown: 977 closed ports
PORT     STATE         SERVICE
21/tcp   open|filtered ftp
22/tcp   open|filtered ssh
23/tcp   open|filtered telnet
25/tcp   open|filtered smtp
53/tcp   open|filtered domain
80/tcp   open|filtered http
111/tcp  open|filtered rpcbind
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
512/tcp  open|filtered exec
513/tcp  open|filtered login
514/tcp  open|filtered shell
1099/tcp open|filtered rmiregistry
1524/tcp open|filtered ingreslock
2049/tcp open|filtered nfs
2121/tcp open|filtered ccproxy-ftp
3306/tcp open|filtered mysql
5432/tcp open|filtered postgresql
5900/tcp open|filtered vnc
6000/tcp open|filtered X11
6667/tcp open|filtered irc
8009/tcp open|filtered ajp13
8180/tcp open|filtered unknown
MAC Address: 08:00:27:4E:BD:0A (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.62 seconds
root@k3s-server:~# nmap -sU 192.168.0.199
Starting Nmap 7.80 ( https://nmap.org ) at 2024-12-21 13:58 MSK
```

Фильтрация

![скрин](https://github.com/dmitriypronin48/nmap/blob/main/img/z2-1.jpg)
![скрин](https://github.com/dmitriypronin48/nmap/blob/main/img/z2-2.jpg)
![скрин](https://github.com/dmitriypronin48/nmap/blob/main/img/z2-3.jpg)
![скрин](https://github.com/dmitriypronin48/nmap/blob/main/img/z2-4.jpg)
