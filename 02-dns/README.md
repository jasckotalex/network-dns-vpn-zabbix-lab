# DNS-сервис BIND9

В лаборатории используется Ubuntu Server с BIND9. DNS-сервер имеет адрес `10.10.20.10` и обслуживает внутреннюю DNS-зону `corp.test`.

## Назначение

Сервер выполняет две функции:

1. Отвечает за локальную зону `corp.test`.
2. Пересылает внешние DNS-запросы через forwarders `8.8.8.8` и `1.1.1.1`.

## Основные параметры

```text
DNS server: 10.10.20.10
DNS port: 53 UDP/TCP
Internal zone: corp.test
Authoritative record: dns.corp.test → 10.10.20.10
Forwarders: 8.8.8.8, 1.1.1.1
```

## Разрешённые сети

BIND принимает DNS-запросы и рекурсивные запросы только от внутренних сетей:

- `10.10.10.0/24` — HQ;
- `10.10.20.0/24` — серверная сеть;
- `10.20.10.0/24` — филиал;
- `localhost` — локальный сервер.

Внешняя сеть VMware `192.168.96.0/24` не добавлена в список разрешённых клиентов.

## Файлы конфигурации

- `named.conf.options` — глобальные параметры BIND;
- `named.conf.local` — подключение локальной зоны;
- `db.corp.test` — записи зоны `corp.test`;
- `validation-commands.md` — команды проверки и диагностики.

## Примеры имён

Внутренняя запись:

```text
dns.corp.test. IN A 10.10.20.10
```

Проверка внутреннего имени:

```bash
dig @10.10.20.10 dns.corp.test A
```

Проверка внешнего имени через локальный BIND:

```bash
dig @10.10.20.10 archive.ubuntu.com A
```

## Результат

Проверено разрешение внутреннего имени `zabbix.corp.test` и внешнего имени `archive.ubuntu.com` через локальный BIND. Для ограничения доступа используется сетевой firewall Linux; подтверждённое правило доступа агента описано в разделе `03-security`.

## Подтверждающие скриншоты

- `01-serverdns-systemctl-named.png` — служба BIND9 active/running и загруженные зоны;
- `02-serverdns-dig-corp-test.png` — локальное имя `zabbix.corp.test` разрешается в `10.10.20.20`;
- `03-serverdns-dig-external-domain.png` — внешний запрос к `archive.ubuntu.com` успешно обработан через forwarder.
