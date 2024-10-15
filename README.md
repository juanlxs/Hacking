# DarkHole I

Este documento se centra en el análisis y la explotación de la máquina "Darkhole I", diseñada específicamente para practicar y mejorar habilidades en ciberseguridad. Solo sabemos que es una máquina Ubuntu 22.04.2 LTS.

**Máquina atacante**: Kali Linux

**Maquina Darkhole I**: [Enlace VulnHUb](https://www.vulnhub.com/entry/darkhole-1,724/)

## 1. Análisis de Red Local con `arp-scan`

Para realizar un escaneo de la red local y detectar dispositivos conectados, se utilizó el comando `arp-scan`. Esta herramienta permite descubrir direcciones IP y MAC en la misma red local mediante la técnica de ARP (Address Resolution Protocol).

```
sudo arp-scan -I eth0 --localnet 
```

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

- `sudo`: Ejecuta el comando con privilegios de superusuario, necesario para realizar el escaneo de ARP.
- `arp-scan`: Nombre de la herramienta utilizada para el escaneo.
- `-I eth0`: Especifica la interfaz de red a utilizar, en este caso `eth0`.
- `--localnet`: Indica que se debe escanear la red local.

**Resultados del Escaneo**:

```
Interface: eth0, type: EN10MB, MAC: 00:0c:29:33:83:21, IPv4: 192.168.190.100
```

- *Interface*: `eth0` es la interfaz de red utilizada para el escaneo.
- *MAC*: `00:0c:29:33:83:21` es la dirección MAC de la interfaz de red del escáner.
- *IPv4*: `192.168.190.100` es la dirección IP asignada a esta interfaz.

**Resumen del escaneo**:

```
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
```

Indica que el escáner comenzó con un total de 256 direcciones IP en el rango especificado.

**Resultados detectados**:


| Dirección IP      | Dirección MAC           | Fabricante          |
|-------------------|-------------------------|----------------------|
| 192.168.190.1     | 00:50:56:c0:00:08      | VMware, Inc.        |
| 192.168.190.2     | 00:50:56:ed:93:5d      | VMware, Inc.        |
| 192.168.190.130   | 00:0c:29:95:75:d5      | VMware, Inc.        |
| 192.168.190.254   | 00:50:56:ec:76:99      | VMware, Inc.        |
| 192.168.190.1     | 00:50:56:c0:00:08      | VMware, Inc. (DUP: 2) |


Se identificaron cuatro dispositivos en la red, todos fabricados por VMware, Inc.

La entrada duplicada para la dirección IP `192.168.190.1` indica que el dispositivo respondió más de una vez durante el escaneo, lo que puede deberse a configuraciones de red específicas o a la existencia de múltiples interfaces.

### 1.1. Archivos de Referencia para `arp-scan`

Al utilizar `arp-scan`, es común referirse a dos archivos que contienen información sobre la asignación de direcciones MAC a sus respectivos fabricantes. Estos archivos son:

1. **`/usr/share/arp-scan/ieee-oui.txt`**:
   - Este archivo contiene la lista de códigos OUI (Organizationally Unique Identifier) asignados por la IEEE (Institute of Electrical and Electronics Engineers). Cada OUI es un prefijo de 24 bits que identifica de manera única a un fabricante de dispositivos de red. El archivo se utiliza para ayudar a identificar el fabricante correspondiente de una dirección MAC cuando se realiza un escaneo de ARP.

2. **`/etc/arp-scan/mac-vendor.txt`**:
   - Este archivo también contiene información sobre los fabricantes de dispositivos de red, pero a menudo se encuentra en un formato más simplificado. Incluye una lista de direcciones MAC y sus correspondientes nombres de fabricante, facilitando la identificación de dispositivos durante el análisis de la red.

Estos archivos son esenciales para interpretar los resultados obtenidos con `arp-scan`, ya que permiten a los usuarios entender mejor la composición de la red al identificar los fabricantes de los dispositivos conectados.

## 2. Análisis de Conectividad con `ping`

Para analizar la conectividad de diferentes direcciones IP en la red utiliza el comando `ping`. Este comando se utiliza para verificar si un host es accesible en una red IP y mide el tiempo de respuesta de los paquetes enviados.

```
┌──(kali㉿kali)-[~]
└─$ ping 192.168.190.1 -c4
PING 192.168.190.1 (192.168.190.1) 56(84) bytes of data.
64 bytes from 192.168.190.1: icmp_seq=1 ttl=128 time=0.453 ms
64 bytes from 192.168.190.1: icmp_seq=2 ttl=128 time=0.465 ms
64 bytes from 192.168.190.1: icmp_seq=3 ttl=128 time=0.425 ms
64 bytes from 192.168.190.1: icmp_seq=4 ttl=128 time=0.426 ms

--- 192.168.190.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3076ms
rtt min/avg/max/mdev = 0.425/0.442/0.465/0.017 ms
```

```
┌──(kali㉿kali)-[~]
└─$ ping 192.168.190.2 -c4
PING 192.168.190.2 (192.168.190.2) 56(84) bytes of data.
64 bytes from 192.168.190.2: icmp_seq=1 ttl=128 time=0.195 ms
64 bytes from 192.168.190.2: icmp_seq=2 ttl=128 time=0.446 ms
64 bytes from 192.168.190.2: icmp_seq=3 ttl=128 time=0.234 ms
64 bytes from 192.168.190.2: icmp_seq=4 ttl=128 time=0.421 ms

--- 192.168.190.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3061ms
rtt min/avg/max/mdev = 0.195/0.324/0.446/0.110 ms
```

```
┌──(kali㉿kali)-[~]
└─$ ping 192.168.190.130 -c4
PING 192.168.190.130 (192.168.190.130) 56(84) bytes of data.
64 bytes from 192.168.190.130: icmp_seq=1 ttl=64 time=0.407 ms
64 bytes from 192.168.190.130: icmp_seq=2 ttl=64 time=0.632 ms
64 bytes from 192.168.190.130: icmp_seq=3 ttl=64 time=0.600 ms
64 bytes from 192.168.190.130: icmp_seq=4 ttl=64 time=0.691 ms

--- 192.168.190.130 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3071ms
rtt min/avg/max/mdev = 0.407/0.582/0.691/0.106 ms
```

```
┌──(kali㉿kali)-[~]
└─$ ping 192.168.190.254 -c4
PING 192.168.190.254 (192.168.190.254) 56(84) bytes of data.

--- 192.168.190.254 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3077ms
```

Nuestra máquina objetivo es 192.168.190.130. **¿Por qué lo sabemos?**

El valor de TTL (Time to Live) en los paquetes ICMP (como los que se utilizan con el comando ping) puede proporcionar información sobre el sistema operativo del host de destino. En este caso, el TTL de `192.168.190.130` es 64, lo que es un indicativo común de que el sistema operativo es Linux. Aquí hay una explicación más detallada:

**¿Qué es el TTL?**

TTL (Time to Live) es un campo en el encabezado de los paquetes IP que se utiliza para evitar que los paquetes circulen indefinidamente en la red. Cada vez que un paquete pasa por un router, su valor de TTL se decrementa en uno. Cuando el TTL llega a cero, el paquete es descartado.

**TTL y Sistemas Operativos**

El valor inicial de TTL puede variar según el sistema operativo. Aquí hay una tabla de referencia comúnmente citada para varios sistemas operativos:

| Sistema Operativo | Valor TTL Inicial |
|-------------------|-------------------|
| Windows           | 128               |
| Linux             | 64                |
| macOS             | 64                |
| FreeBSD           | 255               |
| Solaris           | 255               |
| Cisco IOS         | 255               |

**Conclusión**

El TTL puede ser una pista útil para determinar el sistema operativo de un dispositivo remoto. Sin embargo, es importante recordar que este método no es infalible, ya que el valor de TTL puede ser modificado por configuraciones de red o políticas de seguridad. Aun así, en la mayoría de los casos, un TTL de `64` es un buen indicador de que el sistema operativo es Linux.

## 3. Estructura de Directorios

Crea una estructura de directorios en tu máquina que sea la siguiente:

```
192.168.190.130
├── content
├── exploits
└── nmap
```

- content: Este directorio puede ser utilizado para almacenar cualquier contenido relacionado con la máquina objetivo, como archivos de configuración, scripts, o documentación.

- exploits: Aquí puedes almacenar cualquier exploit que encuentres o desarrolles específicamente para la máquina "Darkhole I".

- nmap: Este directorio puede contener resultados de escaneos realizados con nmap, lo que te ayudará a mantener un registro de los puertos y servicios identificados en la máquina objetivo.

En mi caso la creado con la función `mkt`:

```
┌──(kali㉿kali)-[~]
└─$ mkdir 192.168.190.130

┌──(kali㉿kali)-[~]
└─$ cd 192.168.190.130

┌──(kali㉿kali)-[~/192.168.190.130]
└─$ mkt

┌──(kali㉿kali)-[~/192.168.190.130]
└─$ ls .
content  exploits  nmap
```

### 3.1. Análisis del Escaneo `nmap`

```
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 192.168.190.130 -oG allPorts
```

```
┌──(kali㉿kali)-[~/192.168.190.130/nmap]
└─$ sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 192.168.190.130 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-13 17:12 EDT
Initiating ARP Ping Scan at 17:12
Scanning 192.168.190.130 [1 port]
Completed ARP Ping Scan at 17:12, 0.04s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 17:12
Scanning 192.168.190.130 [65535 ports]
Discovered open port 22/tcp on 192.168.190.130
Discovered open port 80/tcp on 192.168.190.130
Completed SYN Stealth Scan at 17:12, 3.27s elapsed (65535 total ports)
Nmap scan report for 192.168.190.130
Host is up, received arp-response (0.00014s latency).
Scanned at 2024-10-13 17:12:17 EDT for 3s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 64
80/tcp open  http    syn-ack ttl 64
MAC Address: 00:0C:29:95:75:D5 (VMware)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 3.42 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.621MB)
```

**Desglose del comando**:

- `sudo`: Ejecuta el comando con privilegios de superusuario, necesario para realizar escaneos de red avanzados.
- `nmap`: Nombre de la herramienta utilizada para escanear redes y descubrir hosts y servicios.
- `-p-`: Escanea todos los puertos TCP (del 1 al 65535).
- `--open`: Muestra solo los puertos que están abiertos, omitiendo los cerrados en la salida.
- `-sS`: Realiza un escaneo SYN (Stealth Scan), enviando paquetes SYN para determinar el estado de los puertos.
- `--min-rate 5000`: Establece la tasa mínima de paquetes a enviar por segundo a 5000, acelerando el escaneo.
- `-vvv`: Aumenta el nivel de verbosidad a tres, proporcionando información detallada sobre el escaneo.
- `-n`: Desactiva la resolución de nombres DNS, acelerando el escaneo al no resolver direcciones IP.
- `-Pn`: Desactiva la detección de host, asumiendo que el host está activo sin un escaneo previo.
- `192.168.190.130`: Dirección IP del host a escanear, en este caso, la máquina Darkhole I.
- `-oG allPorts`: Guarda la salida en formato "grepable" en un archivo llamado allPorts, permitiendo análisis posterior.

**Resultados del Escaneo**

- Host: La dirección IP 192.168.190.130 está activa, como indica el mensaje de respuesta ARP.
- Puertos Escaneados: Se escanearon todos los 65535 puertos TCP.
- Puertos Abiertos:
   - 22/tcp; Estado: Abierto && Servicio: SSH (OpenSSH 8.2p1 Ubuntu)
   - 80/tcp; Estado: Abierto && Servicio: HTTP (Apache httpd 2.4.41)
- Puertos Cerrados: Se informa que 65533 puertos están cerrados, lo que significa que no respondieron a los paquetes SYN enviados durante el escaneo.
- MAC Address: La dirección MAC del host escaneado es 00:0C:29:95:75, que pertenece a VMware.

Abrimos el archivo `allPorts`: 

```
┌──(kali㉿kali)-[~/192.168.190.130/nmap]
└─$ cat allPorts
# Nmap 7.94SVN scan initiated Sun Oct 13 17:12:17 2024 as: nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn -oG allPorts 192.168.190.130
# Ports scanned: TCP(65535;1-65535) UDP(0;) SCTP(0;) PROTOCOLS(0;)
Host: 192.168.190.130 ()        Status: Up
Host: 192.168.190.130 ()        Ports: 22/open/tcp//ssh///, 80/open/tcp//http///        Ignored State: closed (65533)
# Nmap done at Sun Oct 13 17:12:20 2024 -- 1 IP address (1 host up) scanned in 3.42 seconds
```

Y con la función `extractPorts` resumira lo importante:

```
extractPorts allPorts 
```

```
┌──(kali㉿kali)-[~/192.168.190.130/nmap]
└─$ extractPorts allPorts 

[*] Extracting information...

        [*] IP Address: 192.168.190.130
        [*] Open ports: 22,80

[*] Ports copied to clipboard
```

El siguiente escaneo con nmap proporciona información detallada sobre los puertos abiertos y las versiones de los servicios en ejecución en la máquina "Darkhole I" (192.168.190.130).

```
sudo nmap -sC -sV -p22,80 192.168.190.130 -oN targeted
```

```
┌──(kali㉿kali)-[~/192.168.190.130/nmap]
└─$ sudo nmap -sC -sV -p22,80 192.168.190.130 -oN targeted                           
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-15 13:11 EDT
Nmap scan report for 192.168.190.130
Host is up (0.0014s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e4:50:d9:50:5d:91:30:50:e9:b5:7d:ca:b0:51:db:74 (RSA)
|   256 73:0c:76:86:60:63:06:00:21:c2:36:20:3b:99:c1:f7 (ECDSA)
|_  256 54:53:4c:3f:4f:3a:26:f6:02:aa:9a:24:ea:1b:92:8c (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: DarkHole
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.83 seconds
```

**Desglose del comando** (lo importante):

- `-sC`: Realiza un escaneo de scripts predeterminados.
- `-sV`: Habilita la detección de versiones.
- `-oN targeted`: Especifica la salida del escaneo.

**Resumen y Observaciones**

Sistema Operativo: El servicio Nmap indica que el sistema operativo es Linux.

Puertos Abiertos: Los puertos 22 (SSH) y 80 (HTTP) están accesibles, lo que sugiere que puedes intentar acceder a la máquina a través de SSH o explorar la interfaz web alojada en el puerto 80.

Potenciales Puntos de Ataque:

- El puerto 22 (SSH) es un posible punto de entrada para ataques de fuerza bruta o explotación de vulnerabilidades.
- El puerto 80 (HTTP) sugiere que hay un servidor web corriendo, y se puede realizar un análisis más profundo para descubrir vulnerabilidades en la aplicación web.

Y para ver que distribución de Ubuntu nos encontramos, buscaremos en el navegador:

```
launchpad OpenSSH 8.2p1 Ubuntu 4ubuntu0.2
```

```
launchpad Apache httpd 2.4.41
```

Al buscar estas versiones, deberías encontrar información que te indique que es un Ubuntu Focal.


**OJO!!!** Si vemos dos distribuciones distintas, a lo mejor nos encontramos con contenedores.

Con el comando `whatweb` te proporciona información sobre el sitio web que está corriendo en http://192.168.190.130.

```
whatweb http://192.168.190.130
```

```
┌──(kali㉿kali)-[~/192.168.190.130/nmap]
└─$ whatweb http://192.168.190.130
http://192.168.190.130 [200 OK] Apache[2.4.41], Cookies[PHPSESSID], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[192.168.190.130], Title[DarkHole]
```

**Resumen y Observaciones**

- Servidor Web: Apache 2.4.41
- Sistema Operativo: Ubuntu Linux
- Cookies: Se está utilizando PHPSESSID para la gestión de sesiones.
- Título de la Página: "DarkHole"




