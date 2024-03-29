# Astra preseed install

## TL;DR

1. Ansible:
```shell
apt install -y ansible
```
2. Установочный диск:
```shell
mount /dev/sr0 /media/cdrom
```
3. `ansible-playbook site.yml`.
4. Загрузка клиентских машин.

## Конфигурация

1. Конфигурация сервера установки: `vars/server.yml`.
2. Сценарии установки клиентских машин: `vars/preseeds.yml`.

## Проблемы

- `dnsmasq` не стартует: `apt remove dnsmasq-base`;
- из BIOS нельзя загрузиться с нужной сетевой карты: загрузиться с `ipxe.iso` и загрузиться с нужной карты оттуда (см. раздел IPXE);
- `dnsmasq-tftp: error 8 User aborted the transfer received from 10.1.777.777`: Secure Boot;
- no free leases or rejected:
```shell
sudo ansible-playbook site.yml --tags=remove_dhcp_leases
```
- сеть не работает после установки сервера раздачи: поднять сеть. По умолчанию Astra ставится с интерфейсом `enp*`, после загрузки используются `eth*`, и `/etc/network/interfaces` пустой:
```shell
auto eth0
iface eth0 inet static
address 10.1.777.777
netmask 255.255.777.0
gateway 10.1.777.777
dns-nameserver 192.168.777.777
```

## TODO

* Описание настройка разделов;
* `postinstall.sh` для ssh-ключей;
* деинсталляция сервера раздачи.

## IPXE

1. Загрузка с [ipxe.iso](https://boot.ipxe.org/ipxe.iso).
2. Ctrl-B, консоль IPXE.
3. `ifstat`: список интерфейсов, должен соответствовать списку Linux.
4. `autoboot net[X]`.

На данный момент возникают ошибки `syslinux`, которые предлагается игнорировать. Время ожидания до 5 минут.

## Возможности

 - Раздача репозитория операционной системы;
 - Создание сценариев установки (preseed-файлов) с помощью упрощённого синтаксиса. На данный момент реализована поддерка Astra Linux; в планах Debian;
 - Поддержка программного RAID и LVM;
 - Привязка IP-адресов к MAC-адресам;
 - Использование разных сценариев установки для разных узлов (привязка к MAC-адресам);
 - UEFI. Классическая загрузка на данный момент не протестирована.

## Сценарий установки ОС (preseed-файл)

Preseed-файл задает параметры автоматической установки Debian-подобных систем. На данный момент дополнительные конфигурации `preseed` создаются добавлением объектов второго уровня в объект `preseeds` в файле `vars/preseed.yml`.

## Используемые пакеты:

- Ansible версии 1.7+;
- Dnsmasq: сервер DHCP / TFTP;
- LigHTTPd/Apache 2/vsftpd: сервер раздачи репозитория и preseed-файлов.

## Лицензия

Все материалы распространяются на условиях стандартной общественной лицензии [GNU (GPL)](http://www.gnu.org/copyleft/gpl.html) версии 3. Полный текст лицензии находится в файле LICENSE.

## Авторские права

* Лаборатория 50, http://лаборатория50.рф, team@lab50.net;
* EugeneTM, 2024.

## Благодарности

Выражаю благодарность сотрудникам Лаборатории 50 за подготовленный репозиторий и шаблонизирование preseed-файла. Оригинальный репозиторий: [https://github.com/laboratory50/astra_preseed_install](https://github.com/laboratory50/astra_preseed_install).
