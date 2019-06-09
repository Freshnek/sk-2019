Zadanie 1
---------

![zadanie 1](zadanie-1.svg)

1. Zaprojektuj oraz przygotuj prototyp rozwiązania z wykorzystaniem oprogramowania ``VirtualBox`` lub podobnego. 
Zaproponuj rozwiązanie spełniające poniższe wymagania:
   * Usługodawca zapewnia domunikację z siecią internet poprzez interfejs ``eth0`` ``PC0``
   * Zapewnij komunikację z siecią internet na poziomie ``LAN1`` oraz ``LAN2``
   * Dokonaj takiego podziału sieci o adresie ``172.22.128.0/17`` aby w ``LAN1`` można było zaadresować ``500`` adresów natomiast w LAN2 ``5000`` adresów    
   * Przygotuj dokumentację powyższej architektury w formie graficznej w programie ``DIA``
   
----------------
 
Rozwiązanie
--------------

Podział sieci
-------------
| sieć | adres |
|:-----|:------|
| LAN1 | 172.22.144.0/23 |
| LAN2 | 172.22.128.0/19 |


PC0
---
|  interfejs   | adres  |
|:-------------| :------| 
| eth0 | zapewnia usługodawca |
| eth1 | 172.22.144.1/23  |
| eth2 | 172.22.128.1/19  |

PC1
---
|  interfejs   | adres  |
|:-------------| :------| 
| eth0 | 172.22.144.2/23 |

PC2
---
|  interfejs   | adres  |
|:-------------| :------| 
| eth0 | 172.22.128.2/19 |

--------------

Zmiana adresów IP:
------------------
|``ip addr add``|adres ip|``dev``|nazwa interfejsu|urządzenie|
|:------------- |:-------|:------|:---------------|:---------|
|``ip addr add``|172.22.144.1/23|``dev``|enp0s8 (eth1)|PC0|
|``ip addr add``|172.22.128.1/19|``dev``|enp0s9 (eth2)|PC0|
|``ip addr add``|172.22.144.2/23|``dev``|enp0s3 (eth0)|PC1|
|``ip addr add``|172.22.128.2/23|``dev``|enp0s3 (eth0)|PC2|

Wlaczenie przekazywania adresów IP na Kernelu na PC0:
-------------------------------
``echo 1 > /proc/sys/net/ipv4/ip_forward``

Dodanie trasy na PC1 i PC2:  
-------------------------------
|``ip route add default via``|adres IP PC0|urządzenie|
|:---------------------------|:-----------|:---------|
|``ip route add default via``|172.22.128.1|PC2|
|``ip route add default via``|172.22.144.1|PC1|

Udostępnienie internetu dla LAN1 i LAN2
--
na PC1 i PC2 należy zmienić dns z uczelnianego na googlowy:
--
``/etc/resolv.conf`` 

nameserver ``8.8.8.8``

na PC0 należy włączyć przekazywanie internetu na iptables:
--
``iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE``

Zapisywanie ustawień iptables:
--
``iptables-save > /etc/iptables.conf``

``iptables-restore < /etc/iptables.conf``

------------------------------------------------------
Diagram DIA:
------------
![Zadanie1](Zadanie1.svg)
