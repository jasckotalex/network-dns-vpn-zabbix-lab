# Network, DNS, VPN and Zabbix monitoring lab

Учебная лаборатория в EVE-NG/VMware для практики Linux, сетевой маршрутизации, VLAN, DNS, VPN, firewall и мониторинга Zabbix.

## Что реализовано

- HQ и Branch с отдельными пользовательскими и серверными сетями;
- VLAN 10 для пользователей HQ и VLAN 20 для серверов;
- маршрутизация между HQ и Branch;
- BIND9 с внутренней зоной `corp.test`;
- Zabbix Server 7.4.15 на Ubuntu 22.04;
- PostgreSQL для базы Zabbix;
- Zabbix Agent 2 на Server-Zabbix и ServerDNS;
- SNMPv2c на R-HQ с ограничением запросов по IP Zabbix Server;
- доступ к веб-интерфейсу Zabbix через SSH-туннель;
- сбор метрик CPU, памяти, диска и сети с ServerDNS.

## Топология

![Топология сети](01-network/screenshots/topology.png)

## Адресный план

| Сеть или узел | Адрес | Назначение |
|---|---:|---|
| HQ users | `10.10.10.0/24` | пользовательская сеть HQ |
| HQ servers | `10.10.20.0/24` | серверная сеть HQ |
| Branch users | `10.20.10.0/24` | пользовательская сеть филиала |
| R-HQ ↔ ASA-HQ | `10.254.10.0/30` | транзит HQ |
| R-BRANCH ↔ ASA-BRANCH | `10.254.20.0/30` | транзит Branch |
| ASA-HQ outside | `198.51.100.2/30` | внешняя сторона HQ |
| ISP-Router Gi0/1 | `198.51.100.1/30` | внешняя сторона HQ |
| ASA-BRANCH outside | `198.51.100.6/30` | внешняя сторона Branch |
| ISP-Router Gi0/2 | `198.51.100.5/30` | внешняя сторона Branch |
| ServerDNS | `10.10.20.10` | BIND9 и Zabbix Agent 2 |
| Server-Zabbix | `10.10.20.20` | Zabbix Server, PostgreSQL, Nginx |

## Документация

- [Сетевая часть](01-network/README.md)
- [DNS BIND9](02-dns/README.md)
- [Безопасность и VPN](03-security/README.md)
- [Мониторинг Zabbix](04-zabbix/README.md)
- [Сводная документация](05-documentation/README.md)

## Структура проекта

```text
01-network/          базовая сеть, VLAN и маршрутизация
02-dns/              BIND9 и зона corp.test
03-security/         ASA, NAT, ACL и site-to-site VPN
04-zabbix/           Zabbix Server, Agent 2 и мониторинг
05-documentation/    адресный план и итоговые проверки
```

