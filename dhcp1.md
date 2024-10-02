dhcp1.md
# Exercici 1: Instal.lació d'un servidor DHCP

!!! note "Descripció"

    **Objectiu**: Aprofundir i posar en pràctica els conceptes sobre DHCP
    
    **Desenvolupament**: Individual
    
    **Lliurament**: S'haurà d'entregar documentació


## 1. Preparació de l'escenari

Crear dues màquines a IsardVDI (poden ser server o desktop) que anomenarem `DHCP-Server` i `DHCP-Client`. 
Totes dues han de tenir les les connexions `Default` i `Personal1`, en aquest ordre.

A la màquina anomenada `DHCP-Server` li assignarem una IP estàtica (`192.168.1.100`) al dispositiu de xarxa que no te IP asignada (en principi `enp2s0`)

## 2. Instal.lació del servidor DHCP a la màquina DHCP-Server

Farem servir, per exemple, el servidor ISC DHCP Server, que podem instal.lar de la següent manera:

```sudo apt install -y isc-dhcp-server```

## 3. Configuració del servidor DHCP

Hem d'editar el fitxer de configuración del isc-dhcp-server. El podem trobar a `/etc/dhcp/dhcpd.conf` i aquí tenim un exemple bàsic:

``` ini title="/etc/dhcp/dhcpd.conf"
#/etc/dhcp/dhcpd.conf
default-lease-time 600;
max-lease-time 7200;
    
subnet 192.168.1.0 netmask 255.255.255.0 {
 range 192.168.1.150 192.168.1.200;
 option routers 192.168.1.254;
 option domain-name-servers 192.168.1.1, 192.168.1.2;
 option domain-name "domini.exemple";
}
```

A més, hem de dir-li al nostre servidor, quina tarja de xarxa volem tener a l'escolta per donar les nostres IPs. Aixó ho farem editant el fitxer `/etc/default/isc-dhcp-server` indicant que escoltarem `enp2s0`

```
INTERFACESv4="enp2s0"
```

Finalment reiniciarem el servidor `sudo systemctl restart ics-dhcp-server.service` y verificarem que tot està en ordre amb `sudo systemctl status ics-dhcp-server.service`.
