# Registro de Avances - Plan Intensivo Redes Cisco

Este archivo mantendrá un registro consolidado de los temas, configuraciones y laboratorios que hemos completado satisfactoriamente.

## Progreso General

- [x] Topología Base (Día 0)
- [x] Día 1: IOS + SSH
- [x] Día 2: VLAN + Switch
- [x] Día 3: Inter-VLAN
- [ ] Día 4: STP + EtherChannel
- [ ] Día 5: Routing estático
- [ ] Día 6: OSPF
- [ ] Día 7: NAT/PAT
- [ ] Día 8: ACLs
- [ ] Día 9: Seguridad IOS
- [ ] Día 10: Troubleshooting

---

## Historial de Sesiones

### 📅 CONSOLIDADO 1: Topología y Seguridad Base (Día 0 y 1)

**Hitos Logrados:**
- Diseño e interconexión exitosa de la topología (HQ, Sucursal B, Sucursal C y simulador ISP).
- Inclusión del módulo de expansión Ethernet en R1 HQ.
- Aplicación de mejores prácticas de seguridad, contraseñas encriptadas y SSH en todos los equipos.

**Resumen de Comandos Clave (Día 1):**

| Comando | Explicación |
| --- | --- |
| `hostname [nombre]`<br>*Ej: hostname R1_HQ* | Asigna nombre de sistema. Requisito obligatorio para activar SSH. |
| `no ip domain-lookup`<br>*Ej: no ip domain-lookup* | Evita que la consola se congele (Translating...) por un error de tipeo. |
| `enable secret [clave]`<br>*Ej: enable secret cisco123* | Contraseña maestra para modo privilegiado (totalmente cifrada en la configuración). |
| `service password-encryption`<br>*Ej: service password-encryption* | Protege/ofusca cualquier otra contraseña en texto plano en la configuración del equipo. |
| `username [user] privilege 15 secret [pwd]`<br>*Ej: username admin privilege 15 secret admin1* | Crea usuario administrativo local con máximos privilegios (nivel 15). |
| `ip domain-name [dominio]`<br>*Ej: ip domain-name cisco.com* | Declara dominio. Necesario (junto al hostname) para la semilla de la llave criptográfica. |
| `crypto key generate rsa`<br>*Ej: crypto key generate rsa* | Genera los certificados RSA para cifrar la conexión SSH (normalmente 1024 o 2048 bits). |
| `line vty 0 4`<br>*Ej: line vty 0 4* | Configura el acceso para las 5 terminales virtuales de administración remota de Cisco. |
| `transport input ssh`<br>*Ej: transport input ssh* | Restringe el acceso aceptando SÓLO conexiones seguras SSH (bloquea Telnet). |
| `login local`<br>*Ej: login local* | Obliga a la terminal remota a usar nuestro usuario/contraseña de la database local. |
| `copy running-config startup-config`<br>*Ej: copy run start* | Guarda la configuración de la RAM hacia la NVRAM para no perderla. |

### 📅 CONSOLIDADO 2: VLANs y Switching (Día 2)

**Hitos Logrados:**
- Creación de VLANs 10 (USERS), 20 (ADMIN), 30 (IT) y 99 (MGMT).
- Segmentación de puertos en modo Access para los 14 equipos de la topología.
- Configuración de enlaces Trunk (802.1q) y mitigación de VLAN 1 configurando Native VLAN 99.

**Resumen de Comandos Clave (Día 2):**

| Comando | Explicación |
| --- | --- |
| `vlan [id]` / `name [nombre]`<br>*Ej: vlan 10*<br>*Ej: name USERS* | Ingresa al submodo para crear/nombrar la ID de la red virtual. |
| `switchport mode access`<br>*Ej: switchport mode access* | Fuerza el puerto para conectar un único dispositivo final. |
| `switchport access vlan [id]`<br>*Ej: switchport access vlan 10* | Asigna el puerto de acceso a una VLAN específica. |
| `switchport mode trunk`<br>*Ej: switchport mode trunk* | Convierte el puerto en troncal para pasar múltiples VLANs. |
| `switchport trunk native vlan 99`<br>*Ej: switchport trunk native vlan 99* | Remueve la VLAN 1 como predeterminada por seguridad. |
| `show interfaces trunk`<br>*Ej: show interfaces trunk* | Muestra estado y VLANs nativas de los trunk activos. |
| `show vlan brief`<br>*Ej: show vlan brief* | Muestra qué puertos físicos de acceso están en cada VLAN. |
| `show ip interface brief`<br>*Ej: show ip interface brief* | Muestra el estado (up/down) de todas las interfaces del equipo. |
| `do [comando]`<br>*Ej: do show vlan brief* | Permite ejecutar un comando de modo privilegiado (ej. show, ping, write) sin tener que salir del modo de conf. actual. |

### 📅 CONSOLIDADO 3: Inter-VLAN Routing (Día 3)

**Hitos Logrados:**
- Activación de enrutamiento por hardware (SVI) en el Switch L3 (HQ).
- Diseño de subredes independientes por sucursal para evitar solapamiento (Ej: 192.168.110.X para la Sucursal B).
- Configuración de Router-on-a-Stick (ROAS) en Routers para las Sucursales B y C. *Nota aprendida: La palabra clave `native` en la subinterfaz del router es vital para atrapar el tráfico que el Switch envía SIN etiqueta por el Trunk.*
- Comprobación exitosa de conectividad (ping) entre diferentes VLANs de manera local en cada sucursal.

**Resumen de Comandos Clave (Día 3):**

#### 🎯 Comandos SVI (Switch L3 - HQ)
| Comando | Explicación |
| --- | --- |
| `ip routing`<br>*Ej: ip routing* | Enciende el motor de enrutamiento de capa 3 del switch. |
| `interface vlan [id]`<br>*Ej: interface vlan 10* | Crea una Interfaz Virtual (SVI) para usar como Default Gateway de la red. |
| `ip address [ip] [mask]`<br>*Ej: ip address 192.168.10.1 255.255.255.0* | Asigna la dirección IP y máscara de subred a la interfaz SVI. |
| `no shutdown`<br>*Ej: no shutdown* | Enciende administrativamente la interfaz para permitir el flujo de datos. |

#### 🎯 Comandos Router-on-a-Stick / ROAS (Routers Sucursales)
| Comando | Explicación |
| --- | --- |
| `interface [interfaz].[sub]`<br>*Ej: interface g0/0.10* | Crea una subinterfaz virtual atada al cable físico para la técnica ROAS. |
| `encapsulation dot1Q [id]`<br>*Ej: encapsulation dot1Q 10* | Asocia la subinterfaz a los paquetes que traen la etiqueta (tag) de esa VLAN. |
| `encapsulation dot1Q [id] native`<br>*Ej: encapsulation dot1Q 99 native* | Atrapa el tráfico que el Switch envía SIN etiqueta (desnudo), asumiendo que es la VLAN Nativa. |
| `ip address [ip] [mask]`<br>*Ej: ip address 192.168.110.1 255.255.255.0* | Asigna la dirección IP y máscara de subred a la subinterfaz. |
| `no shutdown`<br>*Ej: no shutdown* | Enciende administrativamente la interfaz para permitir el flujo de datos. |
