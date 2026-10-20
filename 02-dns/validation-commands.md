# Проверка и диагностика DNS

## Проверка конфигурации

Проверить общий синтаксис BIND:

```bash
sudo named-checkconf
```

Если команда ничего не вывела, синтаксис корректный.

Проверить конкретную DNS-зону:

```bash
sudo named-checkzone corp.test /etc/bind/db.corp.test
```

Ожидаемый результат:

```text
zone corp.test/IN: loaded serial 2026101701
OK
```

## Проверка службы

```bash
sudo systemctl status named --no-pager
```

Применить изменения без полной остановки BIND:

```bash
sudo systemctl reload named
```

Проверить ошибки службы:

```bash
sudo journalctl -u named -n 50 --no-pager
```

## Проверка порта 53

```bash
sudo ss -lntup | grep ':53'
```

Ожидается прослушивание DNS на `10.10.20.10:53` по UDP и TCP.

## Проверка внутренних записей

```bash
dig @10.10.20.10 dns.corp.test A
```

Успешный ответ должен содержать:

```text
status: NOERROR
ANSWER: 1
dns.corp.test. 3600 IN A 10.10.20.10
```

## Проверка внешнего разрешения

```bash
dig @10.10.20.10 archive.ubuntu.com A
```

Здесь запрос проходит через локальный BIND и его forwarders.

Для сравнения можно обратиться напрямую к внешнему DNS:

```bash
dig @8.8.8.8 archive.ubuntu.com A
```

## Наблюдение за DNS-трафиком

```bash
sudo tcpdump -ni ens3 -vvv port 53
```

Правильный запрос из HQ должен выглядеть примерно так:

```text
10.10.10.10.xxxxx > 10.10.20.10.53
```

Ответ:

```text
10.10.20.10.53 > 10.10.10.10.xxxxx
```

## Изменение DNS-зоны

После добавления или изменения записи нужно увеличить serial в SOA:

```text
2026101702
```

Затем выполнить:

```bash
sudo named-checkzone corp.test /etc/bind/db.corp.test
sudo rndc reload corp.test
```

Если изменялся `named.conf.options` или `named.conf.local`, использовать:

```bash
sudo named-checkconf
sudo systemctl reload named
```
