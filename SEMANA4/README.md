# Semana 4, Configuraciones básicas de switches

## Configuración de un switch

# Acceder al modo de usuario privilegiado
Para configurar un switch, primero debemos acceder al modo de usuario privilegiado. Para ello, ingresamos el siguiente comando en el modo de usuario:
```bash
enable
```

# Acceder al modo de configuración global
Para configurar un switch, primero debemos acceder al modo de configuración global. Para ello, ingresamos el siguiente comando en el modo de usuario privilegiado:
```bash
enable
configure terminal
```

# Configurar el nombre del switch
```bash
hostname SW1
```
donde `SW1` es el nombre que deseen asignarle al switch.

# Configurar la contraseña de acceso al switch
```bash
enable secret [contraseña]
```
donde `[contraseña]` es la contraseña que deseen asignarle al switch para acceder al modo de configuración.

# Evitar búsqueda de DNS
```bash
no ip domain-lookup
``` 
Esto evitará que el switch intente resolver nombres de dominio cuando se ingresen comandos incorrectos. Esto se realiza en el modo de usuario privilegiado. Para ejecutarlo en el modo de configuración global, se debe ingresar el siguiente comando:
```bash
do no ip domain-lookup
```