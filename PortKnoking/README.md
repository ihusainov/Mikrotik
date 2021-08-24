```bash
/ip firewall raw add action=add-src-to-address-list address-list=knock1 address-list-timeout=2s chain=prerouting dst-address=1.1.1.1 packet-size=128 protocol=icmp
/ip firewall raw add action=add-src-to-address-list address-list=knock2 address-list-timeout=2s chain=prerouting dst-address=1.1.1.1 packet-size=228 protocol=icmp src-address-list=knock1
/ip firewall raw add action=add-src-to-address-list address-list=knock3 address-list-timeout=2s chain=prerouting dst-address=1.1.1.1 packet-size=328 protocol=icmp src-address-list=knock2
/ip firewall raw add action=add-src-to-address-list address-list=whitelist address-list-timeout=1h chain=prerouting dst-address=1.1.1.1 packet-size=578 protocol=icmp src-address-list=knock3
```


**При использовании стандартного файерволла вверху присутствует правило**
```bash
add action=accept chain=input comment="defconf: accept established,related,untracked" connection-state=established,related,untracked. 
```

**При прохождении первого icmp пакета стейтфул файервол микротика создает запись в Connections на 10 секунд.
Соответственно, в течение этого времени icmp пакеты с сорс ip будут проходить файерволл по этому самому правилу, т.к. connection-state=established.
До добавленных правил просто дело не доходит. И чтобы примеры заработали нужно или 11 секунд между пингами, чтобы запись в Connections удалилась 
(но это сильно растягивает время выполнения скрипта на клиенте), либо нужно добавить в первое правило исключение для icmp:** 
```bash
add action=accept chain=input comment="defconf: accept established,related,untracked" connection-state=established,related,untracked protocol=!icmp
```
**Ок, поставил паузу в 11 секунд**

**Утилита для android**
```bash
Google Play (https://play.google.com/store/apps/details?id=me.impa.knockonports)
Knock on Ports - Apps on Google Play
"Knock on Ports" is a port knocking client compatible with knockd, icmpKNOCK and other port knocking servers.
```
