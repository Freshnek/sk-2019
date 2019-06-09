# Zadanie 2

## Projekt sieci lokalnej dla jednostki dydaktycznej uniwersytetu

![budynek](budynek.svg)

### Cel Projektu
  Zaprojektowanie i weryfikacja działania sieci w środowisku testowym. 
  Rozwiązanie zapewnia dostęp do internetu dla wszystkich urządzeńw infrastrukturze.
  
### Założenia projektu

* Sieć zlokalizowana jest w budynku 3 kondygnacyjnym
* Na kążdej z kondygnacji znajdują się laboratoria komputerowe kolejno:
  * poziom 0 
    * 009, 013, 014
  * poziom 1
    * 115, 116, 117, 122
  * poziom 2
    * 201, 202, 203 
* Każde z laboratoriów wyposażone jest w 35 stanowisk dla uczestników kursów
* Jednostka planuje otworzenie kolejnych laboratoriów 017 oraz 204
* Każda kondygnacja wyposażona jest w izolowaną sieć Wi-Fi, udostępniajacą sieć internet podłączonym gościom
  * Sieć Wi-Fi nie pozwala na bezposrednią komunikację z urządzeniami zlokalizowanymi w pozostałej części sieci,
    tj: laboratoria, serwery jednostki
  * Prognozowana maksymalna liczba jednoczesnych urządzeń podłączonych do sieci to ``800``
* Jednostka posiada przyłącze internetowe oraz dysponuje pulą adresów ``188.156.220.160/27``
* Jednostka posiada serwery udostępniajace zasoby do celów dydaktycznych i promocyjnych
  * serwery zlokalizowane są w osobnym pomieszczeniu
  * udostępniają zasoby w sieci publicznej z wykorzystaniem sieci ``188.156.220.160/27``
  * Jeden serwer pełni rolę bramy dla urządzań w sieci lokalnej ``LAN``

### Wstępne założenia

* Każde laboratorium posiada oddzielną podsieć pozwalającą efektywnie zidentyfikować urządzania
  * kondygnacja oraz sala
* Dla uniknięcia zbyt słabego zasięgu sieć WiFi zostanie wyposażona w 4 urządzenia nadawcze na każdej kondygnacji
 

#### zadanie - wymagania

* Dokonaj podziału i projektu sieci w formie dokumentu w formacie ``MARKDOW`` zawierającego specyfikację tekstową oraz obrazkową
  projektowanej sieci
* Przygotuj prototyp rozwiązania z wykorzystaniem oprogramowania ``VirtualBox`` lub podobnego.
* W specyfikacji uwzględnij wielkości sieci oraz ich adresy
* W specyfikacji uwzględnij konfigurację tablicy routingu
* Dokumentację graficzną stworzonej architektury przygotuj w programie ``DIA`` lub podobnym

----------------------------------------------------------------------------------------------------------------------------------

## Rozwiązanie

Podział sieci:
---------------
Sieć w salach: ``188.156.220.160/28`` 
  * Podsieć: ``20.20.0.0/16``
  * Router: ``20.20.0.1/16``
  
WIFI: ``188.156.220.176/28`` 
  * Podsieć: ``40.40.0.0/22``
  * Router: ``40.40.0.1/22``

Poziom 2
------
* Istniejące sale: ``201`` ``202`` ``203``

* Planowane sale: ``204``

* Ilość istniejących stanowisk razem: ``105``

* Ilość planowanych stanowisk razem: ``140``

| Numer sali | Adres sieci |
|:-----------|:------------|
| sala ``201`` | ``20.20.201.0/26`` |
| router sala ``201`` | ``20.20.0.2/26`` ``20.20.201.1/26`` |
| sala ``202`` | ``20.20.202.0/26`` |
| router sala ``202`` | ``20.20.0.3/26`` ``20.20.202.1/26`` |
| sala ``203`` | ``20.20.203.0/26`` |
| router sala ``203`` | ``20.20.0.4/26`` ``20.20.203.1/26`` |
| sala ``204`` | ``20.20.204.0/26`` |
| router sala ``204`` | ``20.20.0.5/26`` ``20.20.204.1/26`` |

Poziom 1
------
* Istniejące sale: ``115`` ``116`` ``117`` ``122``

* Ilość istniejących stanowisk razem: ``140``

| Numer sali | Adres sieci |
|:-----------|:------------|
| sala ``115`` | ``20.20.115.0/26`` |
| router sala ``115`` | ``20.20.0.6/26`` ``20.20.115.1/26`` |
| sala ``116`` | ``20.20.116.0/26`` |
| router sala ``116`` | ``20.20.0.6/26`` ``20.20.116.1/26`` |
| sala ``117`` | ``20.20.117.0/26`` |
| router sala ``117`` | ``20.20.0.7/26`` ``20.20.117.1/26`` |
| sala ``122`` | ``20.20.122.0/26`` |
| router sala ``122`` | ``20.20.0.8/26`` ``20.20.122.1/26`` |

Poziom 0
--------

* Istniejące sale: ``009`` ``013`` ``014``

* Planowane sale: ``017``

* Ilość istniejących stanowisk razem: ``105``

* Ilość planowanych stanowisk razem: ``140``

| Numer sali | Adres sieci |
|:-----------|:------------|
| sala ``009`` | ``20.20.9.0/26`` |
| router sala ``009`` | ``20.20.0.9/26`` ``20.20.9.1/26`` |
| sala ``013`` | ``20.20.13.0/26`` |
| router sala ``013`` | ``20.20.0.10/26`` ``20.20.13.1/26`` |
| sala ``014`` | ``20.20.14.0/26`` |
| router sala ``014`` | ``20.20.0.11/26`` ``20.20.14.1/26`` |
| sala ``017`` | ``20.20.17.0/26`` |
| router sala ``017`` | ``20.20.0.12/26`` ``20.20.17.1/26`` |

#### Łącznie stanowisk planowanych: 420

Diagram DIA
----------

![Zadanie2](Zadania2.svg)

---

Konfiguracja prototypowego rozwiązania
-------------------------------
### Włączenie przekazywania adresów IP na Kernelu:
W pliku: ``/etc/sysctl.d/99-sysctl.conf``

Odkomentować: ``net.ipv4.ip_forward=1``

### Ustawianie iptables dla serwera głównego:
``iptables -t nat -A POSTROUTING -s 188.156.220.160/28 -o enp0s3 -j MASQUERADE``

``iptables -t nat -A POSTROUTING -s 188.156.220.176/28 -o enp0s3 -j MASQUERADE``

### Zapisywanie ustawień iptables dla serwera głównego:
``iptables-save > /etc/iptables.conf``

``iptables-restore < /etc/iptables.conf``

### Ustawienie statycznych adresów IP dla routera WIFI
``` 
auto enp0s3
auto enp0s8
iface enp0s3 inet static
  address 188.156.220.178
  netmask 255.255.255.240
  gateway 188.156.220.177
iface enp0s8 inet static
  address 40.40.0.1
  netmask 255.255.252.0
  gateway 188.156.220.177 
```

### Ustawienie DHCP dla interfejsu routera WIFI

#### Instalacja isc-dhcp-server
``apt install isc-dhcp-server``

#### Konfiguracja isc-dhcp-server

##### W pliku ``/etc/default/isc-dhcp-server``

Odkomentowujemy ``DHCPDv4_CONF``

##### W pliku ``/etc/dhcp/dhcpd.conf`` zapisujemy naszą konfiguracje podsiecie DHCP                

```
subnet 40.40.0.0 netmask 255.255.252.0 {
  option routers 40.40.0.1;
  option subnet-mask 255.255.252.0;
  option domain-name-servers 40.40.0.1;
  range 40.40.0.15 40.40.3.250;
}
```
#### Routing

``ip route add default via 40.40.0.1``

### Ustawienie statycznych adresów IP dla routera dla sal oraz w konkretnej sali:

``` 
auto enp0s3
auto enp0s8
iface enp0s3 inet static
  address 20.20.0.2
  netmask 255.255.0.0
iface enp0s8 inet static
  address 20.20.115.1
  netmask 255.255.255.192
```

### Ustawienie DHCP dla interfejsu dla routera konkretnej sali

#### Instalacja isc-dhcp-server
``apt install isc-dhcp-server``

#### Konfiguracja isc-dhcp-server

##### W pliku ``/etc/default/isc-dhcp-server``

Odkomentowujemy ``DHCPDv4_CONF``

##### W pliku ``/etc/dhcp/dhcpd.conf`` zapisujemy naszą konfiguracje podsiecie DHCP                

```
subnet 20.20.115.0 netmask 255.255.255.192 {
  option routers 20.20.115.1;
  option subnet-mask 255.255.255.192;
  option domain-name-servers 20.20.115.1;
  range 20.20.115.2 20.20.115.63;
}
```
#### Routing

``ip route add default via 20.20.0.1``

``ip route add default via 20.20.115.1``
