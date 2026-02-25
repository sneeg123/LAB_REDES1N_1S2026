# Configuración de vtp y vlan en switches capa 2

## Configuración de VTP
### Configuración de VTP en modo servidor
El modo servidor es el modo predeterminado en los switches Cisco. En este modo, el switch puede crear, modificar y eliminar VLANs, y también puede propagar esta información a otros switches en la misma área de VTP.
```bash
vtp mode server
vtp version 2
vtp domain [nombre_del_dominio]
vtp password [contraseña]
```
### Configuración de VTP en modo cliente
En el modo cliente, el switch no puede crear, modificar ni eliminar VLANs, pero puede recibir información de VLANs de los switches en modo servidor y propagar esta información a otros switches en la misma área de VTP.
```bash
vtp version 2
vtp mode client
vtp domain [nombre_del_dominio]
vtp password [contraseña]
```

### Configuración de VTP en modo transparente
En el modo transparente, el switch no participa en la propagación de información de VLANs, pero puede crear, modificar y eliminar VLANs localmente.
```bash
vtp version 2
vtp mode transparent
vtp domain [nombre_del_dominio]
vtp password [contraseña]
```

## Configuración de VLANs
### Crear una VLAN
```bash
vlan [número_de_vlan]
name [nombre_de_vlan]
```
Ejemplo:
```bash
vlan 10
name Ventas
```
### Eliminar una VLAN
```bash
no vlan [número_de_vlan]
```
Ejemplo:
```bash
no vlan 10
```
## Configuración de interfaces

### Ingresar al modo de configuración de una interfaz
```bash
interface [nombre_de_interface]
```
Ejemplo:
```bash
interface fastEthernet 0/1
```

Para un rango de interfaces consecutivas, se puede usar el siguiente comando:
```bash
interface range fastEthernet 0/1-4
```
### Modo Trunk
```bash
interface [nombre_de_interface]
switchport mode trunk
switchport trunk allowed vlan [números_de_vlan]
```
Ejemplo:
```bash
interface fastEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30
```
### Modo Access
```bash
interface [nombre_de_interface]
switchport mode access
switchport access vlan [número_de_vlan]
```
Ejemplo:
```bash
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
```
### Verificar la configuración de VTP
```bash
show vtp status
```
### Verificar la configuración de VLANs
```bash
show vlan brief
```
### Verificar la configuración de interfaces en modo trunk
```bash
show interfaces [nombre_de_interface] trunk
```