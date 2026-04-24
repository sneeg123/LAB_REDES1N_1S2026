# Comandos para configurar los switches de la topología

Este archivo contiene únicamente la configuración de los switches de la topología, incluyendo:

- Switch multicapa `ESTRELLA` con SVIs.
- Switches de acceso de la estrella: `Switch0`, `Switch1` y `Switch2`.
- Switches inferiores de distribución: `DIST2`.
- Switches inferiores de acceso: `ACCESO_IZQ` y `ACCESO_DER`.
- Uso de VTP en la parte inferior.


---

# 1. Switch multicapa ESTRELLA

En el switch multicapa se crean las VLAN de la parte derecha, se configuran las SVIs y se habilita el enrutamiento con `ip routing`.

```cisco
enable
configure terminal
hostname ESTRELLA
no ip domain-lookup

ip routing

vlan 30
 name VLAN_PC3
vlan 40
 name VLAN_PC4
vlan 50
 name VLAN_PC5

interface GigabitEthernet1/0/1
 no switchport
 ip address 10.0.0.22 255.255.255.252
 no shutdown

interface GigabitEthernet1/0/2
 switchport mode trunk
 switchport trunk allowed vlan 30,40,50
 no shutdown

interface GigabitEthernet1/0/3
 switchport mode trunk
 switchport trunk allowed vlan 30,40,50
 no shutdown

interface GigabitEthernet1/0/4
 switchport mode trunk
 switchport trunk allowed vlan 30,40,50
 no shutdown

interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

interface vlan 40
 ip address 192.168.40.1 255.255.255.0
 no shutdown

interface vlan 50
 ip address 192.168.50.1 255.255.255.0
 no shutdown

router ospf 1
 network 10.0.0.20 0.0.0.3 area 0
 network 192.168.30.0 0.0.0.255 area 0
 network 192.168.40.0 0.0.0.255 area 0
 network 192.168.50.0 0.0.0.255 area 0

end
write memory
```

---

# 2. Switch0

Switch de acceso para PC3. La PC3 estará en la VLAN 30.

```cisco
enable
configure terminal
hostname Switch0

vlan 30
 name VLAN_PC3

interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 30
 no shutdown

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

end
write memory
```

---

# 3. Switch1

Switch de acceso para PC4. La PC4 estará en la VLAN 40.

```cisco
enable
configure terminal
hostname Switch1

vlan 40
 name VLAN_PC4

interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 40
 no shutdown

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 no shutdown

end
write memory
```

---

# 4. Switch2

Switch de acceso para PC5. La PC5 estará en la VLAN 50.

```cisco
enable
configure terminal
hostname Switch2

vlan 50
 name VLAN_PC5

interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 50
 no shutdown

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 50
 spanning-tree portfast
 no shutdown

end
write memory
```

---

# 5. Configuración VTP para la parte inferior

En la parte inferior se usará VTP para administrar las VLAN 10 y 20.

La distribución será:

| Switch | Rol VTP |
|---|---|
| DIST2 | Server |
| ACCESO_IZQ | Client |
| ACCESO_DER | Client |

Datos VTP usados:

```cisco
vtp domain RED_INFERIOR
vtp password cisco123
vtp version 2
```

---

# 6. DIST2 - VTP Server

DIST2 será el servidor VTP. En este switch sí se crean las VLAN 10 y 20.

```cisco
enable
configure terminal
hostname DIST2

vtp domain RED_INFERIOR
vtp password cisco123
vtp version 2
vtp mode server

vlan 10
 name VLAN_USUARIOS_10
vlan 20
 name VLAN_USUARIOS_20

interface FastEthernet0/1
 description Troncal hacia CORE1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface FastEthernet0/2
 description Troncal hacia CORE2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface FastEthernet0/3
 description Troncal hacia ACCESO_IZQ
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface FastEthernet0/4
 description Troncal hacia ACCESO_DER
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

end
write memory
```

---

# 7. ACCESO_IZQ - VTP Client

Switch de acceso del lado izquierdo. Las PCs conectadas aquí usarán VLAN 10 y VLAN 20.

```cisco
enable
configure terminal
hostname ACCESO_IZQ

vtp domain RED_INFERIOR
vtp password cisco123
vtp version 2
vtp mode client

interface FastEthernet0/2
 description Troncal hacia DIST2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface FastEthernet0/3
 description PC0
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface FastEthernet0/4
 description PC1
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

end
write memory
```

---

# 8. ACCESO_DER - VTP Client

Switch de acceso del lado derecho. Las PCs conectadas aquí usarán VLAN 10 y VLAN 20.

```cisco
enable
configure terminal
hostname ACCESO_DER

vtp domain RED_INFERIOR
vtp password cisco123
vtp version 2
vtp mode client

interface FastEthernet0/1
 description Troncal hacia DIST2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface FastEthernet0/3
 description PC2
 switchport mode access
 switchport access vlan 10
 no shutdown

interface FastEthernet0/4
 description PC adicional
 switchport mode access
 switchport access vlan 20
 no shutdown

end
write memory
```

---

# 9. Comandos de verificación

Ejecutar en los switches inferiores para validar VTP:

```cisco
show vtp status
show vlan brief
show interfaces trunk
```

En DIST2 debe aparecer:

```cisco
VTP Operating Mode: Server
VTP Domain Name: RED_INFERIOR
```

En ACCESO_IZQ y ACCESO_DER debe aparecer:

```cisco
VTP Operating Mode: Client
VTP Domain Name: RED_INFERIOR
```

Además, en todos los switches inferiores deben aparecer las VLAN 10 y 20:

```cisco
show vlan brief
```

---

# 10. Verificación en ESTRELLA

Ejecutar en el switch multicapa ESTRELLA:

```cisco
show ip interface brief
show vlan brief
show interfaces trunk
```

---

# 11. Verificación en switches de acceso de la estrella

Ejecutar en Switch0, Switch1 y Switch2:

```cisco
show vlan brief
show interfaces trunk
```

# 12 Configuraciones en Capa 3:

## Router0
```cisco
enable
configure terminal
hostname Router0
no ip domain-lookup

interface Serial2/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown

interface Serial3/0
 ip address 10.0.0.5 255.255.255.252
 no shutdown

interface Serial6/0
 ip address 10.0.0.17 255.255.255.252
 no shutdown

router rip
 version 2
 no auto-summary
 network 10.0.0.0
 redistribute ospf 1 metric 2

router ospf 1
 network 10.0.0.16 0.0.0.3 area 0
 redistribute rip subnets

end
write memory
```

## Router2
```cisco
enable
configure terminal
hostname Router2
no ip domain-lookup

interface Serial2/0
 ip address 10.0.0.18 255.255.255.252
 no shutdown

interface Serial3/0
 ip address 10.0.0.14 255.255.255.252
 no shutdown

interface FastEthernet0/0
 ip address 10.0.0.21 255.255.255.252
 no shutdown

router ospf 1
 network 10.0.0.16 0.0.0.3 area 0
 network 10.0.0.20 0.0.0.3 area 0

end
write memory
```

## CORE1
```cisco
interface FastEthernet1/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.2 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt

interface FastEthernet1/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.2 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 90
 standby 20 preempt

interface Serial2/0
 ip address 10.0.0.2 255.255.255.252

router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 192.168.10.0
 network 192.168.20.0
```

## CORE2
```cisco
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.3 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 90
 standby 10 preempt

interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.3 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 110
 standby 20 preempt

router rip
 version 2
 network 10.0.0.0
 network 192.168.10.0
 network 192.168.20.0
 no auto-summary
```

## ESTRELLA
```cisco
ip routing

interface GigabitEthernet1/0/1
 no switchport
 ip address 10.0.0.22 255.255.255.252
 no shutdown

interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

interface vlan 40
 ip address 192.168.40.1 255.255.255.0
 no shutdown

interface vlan 50
 ip address 192.168.50.1 255.255.255.0
 no shutdown

router ospf 1
 network 10.0.0.20 0.0.0.3 area 0
 network 192.168.30.0 0.0.0.255 area 0
 network 192.168.40.0 0.0.0.255 area 0
 network 192.168.50.0 0.0.0.255 area 0

end
write memory
```
