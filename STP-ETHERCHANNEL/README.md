# Configuración de STP, LACP y PAgP

# 1. Configuración de Spanning Tree Protocol (STP)

STP se utiliza para **evitar bucles en redes con enlaces redundantes**,
seleccionando un **Root Bridge** y bloqueando rutas redundantes cuando
es necesario.

## Forzar el Root Bridge

Se ejecuta en el switch que se desea que sea el **switch raíz de la
red**.

    enable
    conf t
    spanning-tree vlan 10,20 root primary
    end
    write

### Explicación

-   **enable**\
    Accede al modo privilegiado del switch.

-   **conf t**\
    Ingresa al modo de configuración global.

-   **spanning-tree vlan 10,20 root primary**\
    Reduce automáticamente la prioridad del switch para que sea elegido
    como **Root Bridge** en las VLAN indicadas.

-   **end**\
    Sale del modo de configuración.

-   **write**\
    Guarda la configuración en memoria.

## Verificación del Root Bridge

    show spanning-tree

### Explicación

Este comando permite verificar:

-   Qué switch fue elegido como **Root Bridge**
-   El estado de los puertos STP
-   El costo de cada enlace

------------------------------------------------------------------------

# 2. Configuración de EtherChannel con LACP

LACP (**Link Aggregation Control Protocol**) es un protocolo estándar
IEEE 802.3ad que permite **agrupar varios enlaces físicos en un solo
enlace lógico**, aumentando la capacidad y proporcionando redundancia.

## Configuración del EtherChannel (LACP)

    enable
    conf t
    interface range fastEthernet 0/1 - 2
    switchport mode trunk
    channel-group 1 mode active
    no shutdown
    exit

    interface port-channel 1
    switchport mode trunk
    no shutdown
    exit

    end
    write

### Explicación

-   **interface range fastEthernet 0/1 - 2**\
    Selecciona múltiples interfaces para agruparlas.

-   **switchport mode trunk**\
    Configura los puertos como **trunk**, permitiendo tráfico de
    múltiples VLAN.

-   **channel-group 1 mode active**\
    Activa **LACP**, agregando las interfaces al **Port-Channel 1**.

-   **interface port-channel 1**\
    Permite configurar el enlace lógico creado.

-   **no shutdown**\
    Activa la interfaz.

------------------------------------------------------------------------

# 3. Configuración de EtherChannel con PAgP

PAgP (**Port Aggregation Protocol**) es un protocolo propietario de
Cisco que también permite **agrupar enlaces físicos en un solo enlace
lógico**.

## Configuración del EtherChannel (PAgP)

    enable
    conf t
    interface range fastEthernet 0/3 - 4
    switchport mode trunk
    channel-group 2 mode desirable
    no shutdown
    exit

    interface port-channel 2
    switchport mode trunk
    no shutdown
    exit

    end
    write

### Explicación

-   **channel-group 2 mode desirable**\
    Habilita **PAgP en modo activo**, permitiendo negociar la creación
    del EtherChannel.

-   **port-channel 2**\
    Define el enlace lógico que agrupa los enlaces físicos.

------------------------------------------------------------------------

# 4. Verificación de EtherChannel

    show etherchannel summary

### Explicación

Este comando muestra:

-   Los grupos EtherChannel configurados
-   El protocolo utilizado (**LACP o PAgP**)
-   Los puertos que forman parte del canal
-   El estado del canal

### Ejemplo de salida esperada

    Group  Port-channel  Protocol   Ports
    1      Po1           LACP       Fa0/1(P) Fa0/2(P)
    2      Po2           PAgP       Fa0/3(P) Fa0/4(P)

