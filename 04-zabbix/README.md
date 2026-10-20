# Мониторинг Zabbix

## Архитектура

```text
ServerDNS ── Zabbix Agent 2 ── TCP/10050 ── Server-Zabbix
R-HQ ─────── SNMPv2c/UDP 161 ────────────── Server-Zabbix
                                              │
                                              ├─ PostgreSQL
                                              ├─ Nginx/PHP-FPM
                                              └─ Web interface
```

## Установленные компоненты

- `zabbix-server-pgsql` — центральный процесс мониторинга;
- `zabbix-frontend-php` — веб-интерфейс;
- `zabbix-nginx-conf` — конфигурация Nginx;
- `zabbix-sql-scripts` — схема базы данных;
- `zabbix-agent2` — агент для Linux-узлов;
- `postgresql` — база данных Zabbix;
- `php8.1-pgsql` — подключение PHP к PostgreSQL;
- `snmp` — инструменты проверки SNMP.

## Мониторируемые узлы

| Узел | Интерфейс | Метод | Шаблон |
|---|---|---|---|
| Zabbix server | `127.0.0.1:10050` | Zabbix Agent 2 | Linux by Zabbix agent |
| ServerDNS | `10.10.20.10:10050` | Zabbix Agent 2 | Linux by Zabbix agent |
| R-HQ | `10.10.10.1:161` | SNMPv2c | Cisco IOS SNMP |

## Доступ к веб-интерфейсу

В учебной лаборатории веб-интерфейс не публикуется напрямую. Используется локальный SSH-туннель:

```powershell
ssh -N -o ServerAliveInterval=30 -o ServerAliveCountMax=3 -L 18080:127.0.0.1:8080 labadmin@10.10.20.20
```

Затем интерфейс открывается по адресу:

```text
http://127.0.0.1:18080
```

## Проверки

Основные подтверждения этапа находятся в каталоге [`screenshots/`](screenshots/):

1. `01-zabbix-services-running.png` — запущенные сервисы Zabbix, Nginx, PHP-FPM и PostgreSQL;
2. `02-serverdns-agent-running.png` — состояние Zabbix Agent 2 на ServerDNS;
3. `03-zabbix-hosts-available.png` — доступность узлов в интерфейсе Zabbix;
4. `04-serverdns-memory-graph.png` — график использования памяти;
5. `05-serverdns-latest-data.png` — актуальные метрики ServerDNS.

Если файл ещё не добавлен локально, его наличие отмечается как `TODO`, а результат не считается опубликованным.

Проверка агента с Zabbix Server:

```bash
nc -vz -w 3 10.10.20.10 10050
```

Проверка SNMP:

```bash
snmpget -v2c -c <SNMP_COMMUNITY> 10.10.10.1 1.3.6.1.2.1.1.1.0
```

Вместо `<SNMP_COMMUNITY>` нужно подставить значение локально; секреты не следует сохранять в Git.

## Дальнейшие задачи

- добавить ASA-HQ и ASA-BRANCH через SNMP;
- настроить триггеры для диска, памяти, интерфейсов и VPN;
- собрать отдельный dashboard сети;
- настроить уведомления;
- добавить резервное копирование PostgreSQL и конфигураций.
