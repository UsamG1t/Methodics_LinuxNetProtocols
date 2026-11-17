[:contents:]

# Вступление

Для изучения курса, управления виртуальными машинами в рамках лекций и семинаров, а также для выполнения домашних заданий необходимо настраивать сетевые топологии виртуальных машин. В домашнее задание также входит сбор данных в отчёт специального вида с помощью встроенных в виртуальные машины утилит.

В качестве менеджера виртуальных машин используется [VirtualBox](https://www.virtualbox.org/), система для выполнения лабораторных предоставляется в виде [архива с виртуальной машиной](https://cloud.mail.ru/public/LNbQ/Wrh4cAvKe) (Open Virtual Appliance, `.ova`-файл), на основе которого будет создана основная система, на её основе будут создаваться копии для работы.

---

# Настройка виртуальных машин

В данной главе описаны основные действия и / или команды, необходимые для работы с `VirtualBox`.

:information_source: `VirtualBox` поддерживает собственный набор команд для работы из терминала с помощью встроенной утилиты `VBoxManage`, полный список команд представлен по команде `VBoxManage --help`. Также все настройки над виртуальными машинами возможно проводить через графическую оболочку. Данные способы настройки в будущем будут представлены в [отдельном разделе](../Additional/Old_Settings.md).

Для настройки ВМ в данных лабораторных используется пакет [VBoxLab](https://github.com/UsamG1t/Neurovboxlab/tree/update/refactor).

:warning: ***Важное примечание:*** В ранних версиях `VirtualBox` при работе из консоли использовалась локализация внутренних настроек, из-за чего переведённые файлы настроек **не работали**. При работе используйте латинские названия, перед работой _рекомендуется_ менять локаль с помощью команды `export LC_ALL=C`

## Импорт образа и создание виртуальных машин

:information_source: Для выполнения лабораторных необходимо единожды импортировать образ диска и после создавать клоны для работы.

:round_pushpin: 1. Скачайте последнюю версию пакета [`VBoxLab`](https://github.com/UsamG1t/Neurovboxlab/blob/update/refactor/dist/vboxlab-3.0.0-py3-none-any.whl)

:round_pushpin: 2. Установите пакет в вашу систему или в виртуальное окружение Python:

```console
[papillon_rouge@localhost ~]$ python3 -m venv .vboxlab
[papillon_rouge@localhost ~]$ source .vboxlab/bin/activate
(.vboxlab) [papillon_rouge@localhost ~]$ pip install Downloads/vboxlab-3.0.0-py3-none-any.whl
Processing ./Downloads/vboxlab-3.0.0-py3-none-any.whl
…
Successfully installed prompt_toolkit-3.0.53 vboxlab-3.0.0 pyte-0.8.2 wcwidth-0.8.2
(.vboxlab) [papillon_rouge@localhost ~]$
```

:information_source: В дальнейшем для работы с утилитой необходимо будет просто активировать окружение.

```console
[papillon_rouge@localhost ~]$ source .vboxlab/bin/activate
(.vboxlab) [papillon_rouge@localhost ~]$
```

:information_source: Для изучения возможностей установленного пакета воспользуйтесь командой `vboxlab --help`.

```console
(.vboxlab) [papillon_rouge@localhost ~]$ vboxlab --help
usage: vboxlab COMMAND [ARGUMENTS] | vboxlab COMMAND --help

Manage VirtualBox appliance imports and linked clones for network labs: import images, clone and
configure virtual machines, attach to their serial consoles or through ssh and read the session
reports recorded inside them.

options:
  -h, --help     show this help message and exit
  -v, --version  show the VBoxLab version and exit

Virtual machines:
  list                List VirtualBox groups and their virtual machines.
  show                Show the group, serial port, parent and access of a VM.
  import              Import an OVA or OVF appliance and set up its access.
  clone               Create a linked clone with a serial port and networks.
  set                 Show or change the ports, adapters and access of a VM.
  erase               Delete VMs, with dependency checks for linked clones.

Running virtual machines:
  connect             Start a VM headless and attach to its console or ssh.
  ssh-key             Install the VBoxLab ssh key in a guest reached by ssh.
  monitor             List VMs that are running, paused or changing state.
  pause               Pause running VMs; they stay in memory.
  resume              Resume paused VMs.
  stop                Shut VMs down through the ACPI power button.

Session reports:
  report              Read, check, extract and compare student session reports.

Maintenance:
  docs                Open the bundled HTML documentation.
  full-clean          Delete the VirtualBox machine and config directories.
  --integration-test  Run the end-to-end check on a real VirtualBox.

Run "vboxlab COMMAND --help" for the arguments and examples of a command.
```

Система управления ВМ позволяет использовать её не только в рамках курса. Однако для него в нее добавлены флаги ускоренной настройки системы.

:round_pushpin: 3. Установите образ ВМ. Импортируйте установленный образ с помощью команды `vboxlab import --lab-vm`. Используйте `-o короткое_имя` для переименования образа:

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab import -o IPv6_Lab --lab-vm Downloads/protocolsIPv6-jeos-x86_64.ova
Importing [################################] 100%
Imported virtual machines:
- IPv6_Lab (/LinuxNetwork2026): COM1 serial console, port 2329 (lab)
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$
```

:round_pushpin: 4. Для клонирования машины используйте команду `vboxlab clone`. Укажите базовую ВМ и имя для клона:

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab clone IPv6_Lab lab_1
Name: lab_1
Group: /LinuxNetwork2026
Serial port: 2330
Parent VM: IPv6_Lab
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$
```

### Настройка параметров ВМ

Для некоторых ВМ необходимо сразу настраивать подключения к COM-портам или к сетевым адаптерам.

Настройка сетевых адаптеров может проводиться сразу при создании клона:

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab clone IPv6_Lab lab_set intnet deepnet
Name: lab_set
Group: /LinuxNetwork2026
Serial port: 2331
Parent VM: IPv6_Lab
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$
```

Для просмотра текущих настроек ВМ воспользуйтесь командой `vboxlab set`:

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab set --help
usage: vboxlab set [-h] VM [OPTION ...]

Show or change the serial (COM) ports and network adapters of a VM: COM1-COM4, and NIC1-NIC8 or
NIC1-NIC36 depending on the chipset of the VM, and how VBoxLab reaches the guest. Without options the
current settings are shown. Ports and adapters change only on a stopped VM, as does access=com when
COM1 is no TCP server yet; access=ssh, lab and ssh-user can change at any time. All options are
checked first and then applied together, so an invalid option changes nothing. Removing a TCP server
prints a warning.

positional arguments:
  VM          Name of the virtual machine.
  OPTION      KEY=VALUE option, see below.

options:
  -h, --help  show this help message and exit

Options (N is the port or adapter number):
  comN=auto        TCP server on a free port (keeps an existing one)
  comN=PORT        TCP server on the given port
  comN=server:NAME host pipe NAME, this VM creates it
  comN=client:NAME host pipe NAME, this VM connects to it
  comN=off         no serial port
  nicN=NETWORK     internal network NETWORK
  nicN=nat         NAT
  nicN=off         no network adapter
  access=com       reach the guest through the COM1 console; COM1
                   becomes a TCP server on a free port unless it is one
  access=ssh       reach the guest through ssh; a free port is forwarded
                   on the first NAT adapter unless one is forwarded
  lab=yes|no       whether clones get the lab DMI system information
  ssh-user=NAME    guest user for ssh (default: root)

Examples:
  vboxlab set first
  vboxlab set first nic2=left nic3=right com2=server:link
  vboxlab set second nic2=left com2=client:link
  vboxlab set first com1=2330 nic4=off
  vboxlab set first access=ssh ssh-user=student
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$
```

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab set lab_set
lab_set (poweroff)
  Access  COM1 serial console, port 2331 (lab)
  COM1  TCP server, port 2331
  COM2  off
  COM3  off
  COM4  off
  NIC1  NAT
  NIC2  internal network "intnet"
  NIC3  internal network "deepnet"
  NIC4  null
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab set lab_1
lab_1 (poweroff)
  Access  COM1 serial console, port 2330 (lab)
  COM1  TCP server, port 2330
  COM2  off
  COM3  off
  COM4  off
  NIC1  NAT
  NIC2  null
  NIC3  null
  NIC4  null
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab set lab_1 com2=2345 com3=server:channel nic1=intnet
lab_1 (poweroff)
  Access  COM1 serial console, port 2330 (lab)
  COM1  TCP server, port 2330
  COM2  TCP server, port 2345
  COM3  pipe server, creates "channel" (/tmp/vboxlab-channel)
  COM4  off
  NIC1  internal network "intnet"
  NIC2  null
  NIC3  null
  NIC4  null
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$
```

## Подключение к виртуальным машинам

После создания виртуальных машин необходимо их включить и к ним подключиться. Обычный запуск виртуальных машин из графического интерфейса автоматически открывает эмулятор экрана виртуальной машины, установленный в VirtualBox. Однако из-за особенностей настройки он не позволяет производить копирование из основной системы в интерфейс виртуальной машины и, соответственно, копировать данные между виртуальными машинами с помощью буфера обмена.

Воспользуйтесь командой `vboxlab connect` для подключения к ВМ:

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab connect lab_1
```

### Работа с командной строкой

:information_source: В процессе выполнения лабораторных работ необходимо проводить настройку виртуальных машин через терминальный интерфейс. Рассмотрим команды и работу с терминалом на примере тестовой машины.

:round_pushpin: 1. Запустим виртуальную машину. Для входа в систему каждой машины необходимо вводить логин (`root`) и пароль (`root`).

:warning: ***ДИСКРЕЙМЕР:*** Никогда не устанавливайте такие легко угадываемые пароли на свои персональные устройства!

:round_pushpin: 2. Для переименования виртуальной машины используется специальная команда `sethostname` (при смене имени виртуальной машины требуется повторный вход в систему). С помощью данной команды переименуйте ВМ.

`@srv`
```console
localhost login: root
Password:
[root@localhost ~]# sethostname srv


srv login: root
Password:
Last login: Fri Sep  5 13:09:38 UTC 2025 on ttyS0
[root@srv ~]#
```

:large_blue_diamond: При работе с терминалом при наборе команд воспользуйтесь клавишей `Tab` для автодополнения команд. При неоднозначной возможности дополнения после нескольких нажатий `Tab` будет выведены варианты дополнения команд:

`@srv`
```console
[root@srv ~]# c
c_rehash	catman	chcon			choom	chrt	clockdiff
colrm		compopt	control-dump	cpio	ctstat		cal
cd			chcpu	chown			chvt	clrunimap	column
conntrack 	control-restore			crlutil	curl 		caller
cd.. 		chgrp 					chpasswd			cksum
cmp			comm					conntrackd			convertdev.sh
crond		cut		capsh			certutil			chkconfig
chronyc		clear	cmsutil	command	consoletype			convertquota
crontab		cvtsudoers		case	chacl	chmem		chronyd
clock		col		compgen	continue		coproc		csplit
cat			chattr	chmod	chroot	clock_unsynced		colcrt
complete	control	cp		ctrlaltdel
[root@srv ~]# ca
cal			caller	capsh	case	cat		catman
[root@srv ~]# cal
  September 2025
Su Mo Tu We Th Fr Sa
   1  2  3  4  5  6
7  8  9 10 11 12 13
14 15 16 17 18 19 20
21 22 23 24 25 26 27
28 29 30
```

:large_blue_diamond: Нажатие `Tab` в процессе набора команд без введённых символов приводит к выводу возможных аргументов команды. Сочетание клавиш `Ctrl+C` позволяет сбросить введённую команду без её выполнения.
`@srv`
```console
[root@srv ~]# ip l
l2tp  link
[root@srv ~]# ip link
add       afstats   change    delete    help      property  set       show
[root@srv ~]# ip link ^C
[root@srv ~]#
```

:large_blue_diamond: Для просмотра истории команд можно использовать специальную команду `history` (заметим, что не исполненные команды, отмененные по `Ctrl+C`, не указываются в истории):

`@srv`
```console
[root@srv ~]# date
Sat Sep 20 03:06:32 UTC 2025
[root@srv ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
   link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
   inet 127.0.0.1/8 scope host lo
      valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
   link/ether 08:00:27:d2:1c:a7 brd ff:ff:ff:ff:ff:ff
   altname enp0s3
   altname enx080027d21ca7
3: eth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
   link/ether 08:00:27:34:f7:3f brd ff:ff:ff:ff:ff:ff
   altname enp0s8
   altname enx08002734f73f
4: eth2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
   link/ether 08:00:27:09:eb:1f brd ff:ff:ff:ff:ff:ff
   altname enp0s9
   altname enx08002709eb1f
5: eth3: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
   link/ether 08:00:27:6e:55:de brd ff:ff:ff:ff:ff:ff
   altname enp0s10
   altname enx0800276e55de
[root@srv ~]# ip link set eth1 up
[root@srv ~]# history
   1  cal
   2  date
   3  ip a
   4  ip link set eth1 up
   5  history
[root@srv ~]#
```

:large_blue_diamond: Для редактирования введенных команд можно с помощью клавиш ↑ и ↓ просматривать историю команд и модифицировать их.

`@srv`
```console
[root@srv ~]# ip link set ethi up
Cannot find device "ethi"
[root@srv ~]# ip link set eth1 up
[root@srv ~]#
```

:large_blue_diamond: Для поиска по истории команд можно воспользоваться сочетанием клавиш `Ctrl+R`. _После_ нажатия необходимо ввести подстроку интересуемой команды, терминал предложит последнюю команду, содержащую подстроку. Повторное нажатие `Ctrl+R` будет продолжать поиск по истории.

`@srv`
```console
(reverse-i-search)`c<a>l
[root@srv ~]# ca
  September 2025
Su Mo Tu We Th Fr Sa
   1  2  3  4  5  6
7  8  9 10 11 12 13
14 15 16 17 18 19 20
21 22 23 24 25 26 27
28 29 30

[root@srv ~]#
```

## Создание отчета

:information_source: Для сдачи домашних заданий используется специальная утилита `report`, создающая tar-архив с записью команд в системе.

Для этого примера создайте две ВМ: `first` и `second`. Объедините их в одну сеть, настройте на них ip-адреса:

```console
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab clone IPv6_Lab first intnet
<…>
(.vboxlab) [papillon_rouge@BaseALT-Papillon ~]$ vboxlab clone IPv6_Lab second intnet
<…>
```

`@first`
```console
[root@first ~]# ip link set eth1 up
[root@first ~]# ip addr add dev eth1 10.9.0.1/24
```

`@second`
```console
[root@second ~]# ip link set eth1 up
[root@second ~]# ip addr add dev eth1 10.9.0.2/24
```

:round_pushpin: 1. С помощью команды `report <#Lab> <hostname>` запустите запись отчёта на обеих машинах:

`@first`
```console
[root@first ~]# report 1 first
```
```console
Report is started. Task: 01; host: first.
Use exit or ^D to finish.
[root@01-first ~]#
```

`@second`
```console
[root@second ~]# report 1 second
```
```console
Report is started. Task: 01; host: second.
Use exit or ^D to finish.
[root@01-second ~]#
```

:information_source: Затем необходимо будет выполнять указанные в самостоятельной работе команды. Сейчас выполним на каждой виртуальной машине следующие команды:
 + `ip a show eth1`
 + `ping -c5 <адрес другого абонента>`

:round_pushpin: 2. На каждой машине выполните описанные команды в режиме report. Затем завершите запись с помощью команды `exit` или сочетания клавиш `Ctrl+D`

`@first`
```console
[root@01-first ~]# ip a show eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
   link/ether 08:00:27:da:00:dd brd ff:ff:ff:ff:ff:ff
   altname enp0s8
   altname enx080027da00dd
   inet 10.9.0.1/24 scope global eth1
      valid_lft forever preferred_lft forever
[root@01-first ~]# ping -c5 10.9.0.2
PING 10.9.0.2 (10.9.0.2) 56(84) bytes of data.
64 bytes from 10.9.0.2: icmp_seq=1 ttl=64 time=0.531 ms
64 bytes from 10.9.0.2: icmp_seq=2 ttl=64 time=0.635 ms
64 bytes from 10.9.0.2: icmp_seq=3 ttl=64 time=0.712 ms
64 bytes from 10.9.0.2: icmp_seq=4 ttl=64 time=0.558 ms
64 bytes from 10.9.0.2: icmp_seq=5 ttl=64 time=0.927 ms

--- 10.9.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4132ms
rtt min/avg/max/mdev = 0.531/0.672/0.927/0.142 ms
[root@01-first ~]#
<^D>exit
Report is stopped.
[root@first ~]#
```

`@second`
```console
[root@01-second ~]# ip a show eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
   link/ether 08:00:27:e1:50:ee brd ff:ff:ff:ff:ff:ff
   altname enp0s8
   altname enx080027e150ee
   inet 10.9.0.2/24 scope global eth1
      valid_lft forever preferred_lft forever
[root@01-second ~]# ping -c5 10.9.0.1
PING 10.9.0.1 (10.9.0.1) 56(84) bytes of data.
64 bytes from 10.9.0.1: icmp_seq=1 ttl=64 time=0.648 ms
64 bytes from 10.9.0.1: icmp_seq=2 ttl=64 time=0.780 ms
64 bytes from 10.9.0.1: icmp_seq=3 ttl=64 time=0.832 ms
64 bytes from 10.9.0.1: icmp_seq=4 ttl=64 time=0.569 ms
64 bytes from 10.9.0.1: icmp_seq=5 ttl=64 time=0.758 ms

--- 10.9.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4004ms
rtt min/avg/max/mdev = 0.569/0.717/0.832/0.095 ms
[root@01-second ~]# exit
exit
Report is stopped.
[root@second ~]#
```

:information_source: После записи в домашнем каталоге появится файл `report.<#Lab>.<hostname>`. С помощью `ls -l` можно убедиться в предварительной корректности записи (число после слова `root` показывает размер файла, он должен быть ненулевой), а с помощью команды `report <report-name>` можно «проиграть» запись команд.

:round_pushpin: 3. С помощью команды `report <report-name>` запустите получившиеся отчёты

`@first`
```console
[root@first ~]# ls -l
total 12
drwxr-xr-x 2 root root 4096 May 12 17:18 bin
-rw-r--r-- 1 root root 2621 Oct 13 12:28 report.01.first
drwx------ 2 root root 4096 May 12 17:19 tmp

[root@first ~]# report report.01.first
Replay report.01.first
[root@01-first ~]# ip a show eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
   link/ether 08:00:27:da:00:dd brd ff:ff:ff:ff:ff:ff
   altname enp0s8
   altname enx080027da00dd
   inet 10.9.0.1/24 scope global eth1
      valid_lft forever preferred_lft forever
[root@01-first ~]# ping -c5 10.9.0.2
PING 10.9.0.2 (10.9.0.2) 56(84) bytes of data.
64 bytes from 10.9.0.2: icmp_seq=1 ttl=64 time=0.531 ms
64 bytes from 10.9.0.2: icmp_seq=2 ttl=64 time=0.635 ms
64 bytes from 10.9.0.2: icmp_seq=3 ttl=64 time=0.712 ms
64 bytes from 10.9.0.2: icmp_seq=4 ttl=64 time=0.558 ms
64 bytes from 10.9.0.2: icmp_seq=5 ttl=64 time=0.927 ms

--- 10.9.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4132ms
rtt min/avg/max/mdev = 0.531/0.672/0.927/0.142 ms
[root@01-first ~]#
exit

Replay report.01.first finished
[root@first ~]#
```

`@second`
```console
[root@second ~]# ls -l
total 12
drwxr-xr-x 2 root root 4096 May 12 17:18 bin
-rw-r--r-- 1 root root 2498 Oct 13 12:29 report.01.second
drwx------ 2 root root 4096 May 12 17:19 tmp

[root@second ~]# report report.01.second
Replay report.01.second
[root@01-second ~]# ip a show eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
   link/ether 08:00:27:e1:50:ee brd ff:ff:ff:ff:ff:ff
   altname enp0s8
   altname enx080027e150ee
   inet 10.9.0.2/24 scope global eth1
      valid_lft forever preferred_lft forever
[root@01-second ~]# ping -c5 10.9.0.1
PING 10.9.0.1 (10.9.0.1) 56(84) bytes of data.
64 bytes from 10.9.0.1: icmp_seq=1 ttl=64 time=0.648 ms
64 bytes from 10.9.0.1: icmp_seq=2 ttl=64 time=0.780 ms
64 bytes from 10.9.0.1: icmp_seq=3 ttl=64 time=0.832 ms
64 bytes from 10.9.0.1: icmp_seq=4 ttl=64 time=0.569 ms
64 bytes from 10.9.0.1: icmp_seq=5 ttl=64 time=0.758 ms

--- 10.9.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4004ms
rtt min/avg/max/mdev = 0.569/0.717/0.832/0.095 ms
[root@01-second ~]# exit
exit

Replay report.01.second finished
[root@second ~]#
```

## Извлечение отчета из ВМ

Следующим шагом необходимо перенести полученные отчёты из виртуальных машин в основную систему.

:round_pushpin: 1. Не выключая ВМ, в отдельном окне терминала запустите команду `vboxlab report extract VMName REPORT_Name`

```console
(.vboxlab) [papillon_rouge@localhost ~]$ vboxlab report extract first report.01.first
Extracted "$HOME"/report.01.first from first to /home/papillon-rouge/report.01.first (2506 bytes, sha256 22a53940de290b208010e774ab5ec1c10812e0b707f865d4ca3c1790696afbc3).
(.vboxlab) [papillon_rouge@localhost ~]$ vboxlab report extract second report.01.second
Extracted "$HOME"/report.01.second from second to /home/papillon-rouge/report.01.second (2474 bytes, sha256 0b45ad605e04dc8571287ca6ea05e0d8770b32e9ad6e4e4a8eaf5f8e8154186a).
```

:round_pushpin: 2. C помощью команды `vboxlab report text <REPORT_Name>` просмотрите записанный отчет.

```console
(.vboxlab) [papillon_rouge@localhost ~]$ vboxlab report text report.01.first
[root@01-first ~]# ip a show eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
   link/ether 08:00:27:da:00:dd brd ff:ff:ff:ff:ff:ff
   altname enp0s8
   altname enx080027da00dd
   inet 10.9.0.1/24 scope global eth1
      valid_lft forever preferred_lft forever
[root@01-first ~]# ping -c5 10.9.0.2
PING 10.9.0.2 (10.9.0.2) 56(84) bytes of data.
64 bytes from 10.9.0.2: icmp_seq=1 ttl=64 time=0.531 ms
64 bytes from 10.9.0.2: icmp_seq=2 ttl=64 time=0.635 ms
64 bytes from 10.9.0.2: icmp_seq=3 ttl=64 time=0.712 ms
64 bytes from 10.9.0.2: icmp_seq=4 ttl=64 time=0.558 ms
64 bytes from 10.9.0.2: icmp_seq=5 ttl=64 time=0.927 ms

--- 10.9.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4132ms
rtt min/avg/max/mdev = 0.531/0.672/0.927/0.142 ms
[root@01-first ~]#
exit
```
