# DarkHole I

Este documento se centra en el análisis y la explotación de la máquina "Darkhole I", diseñada específicamente para practicar y mejorar habilidades en ciberseguridad.

**Máquina atacante**: Kali Linux

**Maquina Darkhole I**: [Enlace VulnHUb](https://www.vulnhub.com/entry/darkhole-1,724/)

## Análisis de Red Local con arp-scan

Para realizar un escaneo de la red local y detectar dispositivos conectados, se utilizó el comando ```arp-scan```. Esta herramienta permite descubrir direcciones IP y MAC en la misma red local mediante la técnica de ARP (Address Resolution Protocol).

```
┌──(kali㉿kali)-[/etc/arp-scan] 
└─$ sudo arp-scan -I eth0 --localnet 
Interface: eth0, type: EN10MB, MAC: 00:0c:29:33:83:21, IPv4: 192.168.190.100 
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan) 
192.168.190.1   00:50:56:c0:00:08       VMware, Inc. 
192.168.190.2   00:50:56:ed:93:5d       VMware, Inc. 
192.168.190.130 00:0c:29:95:75:d5       VMware, Inc. 
192.168.190.254 00:50:56:ec:76:99       VMware, Inc. 
192.168.190.1   00:50:56:c0:00:08       VMware, Inc. (DUP: 2) 


5 packets received by filter, 0 packets dropped by kernel 
Ending arp-scan 1.10.0: 256 hosts scanned in 1.952 seconds (131.15 hosts/sec). 4 responded 
```

**Desglose del comando**:

- ```sudo```: Ejecuta el comando con privilegios de superusuario, necesario para realizar el escaneo de ARP.
- ```arp-scan```: Nombre de la herramienta utilizada para el escaneo.
- ```-I eth0```: Especifica la interfaz de red a utilizar, en este caso ```eth0```.
- ```--localnet```: Indica que se debe escanear la red local.

**Resultados del Escaneo**:

```
Interface: eth0, type: EN10MB, MAC: 00:0c:29:33:83:21, IPv4: 192.168.190.100
```

- *Interface*: ```eth0``` es la interfaz de red utilizada para el escaneo.
- *MAC*: ```00:0c:29:33:83:21``` es la dirección MAC de la interfaz de red del escáner.
- *IPv4*: ```192.168.190.100``` es la dirección IP asignada a esta interfaz.

**Resumen del escaneo**:

```
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
```

Indica que el escáner comenzó con un total de 256 direcciones IP en el rango especificado.

**Resultados detectados**

### Resultados detectados:

| Dirección IP      | Dirección MAC           | Fabricante          |
|-------------------|-------------------------|----------------------|
| 192.168.190.1     | 00:50:56:c0:00:08      | VMware, Inc.        |
| 192.168.190.2     | 00:50:56:ed:93:5d      | VMware, Inc.        |
| 192.168.190.130   | 00:0c:29:95:75:d5      | VMware, Inc.        |
| 192.168.190.254   | 00:50:56:ec:76:99      | VMware, Inc.        |
| 192.168.190.1     | 00:50:56:c0:00:08      | VMware, Inc. (DUP: 2) |


Se identificaron cuatro dispositivos en la red, todos fabricados por VMware, Inc.

La entrada duplicada para la dirección IP ```192.168.190.1``` indica que el dispositivo respondió más de una vez durante el escaneo, lo que puede deberse a configuraciones de red específicas o a la existencia de múltiples interfaces.





