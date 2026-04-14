# Plan Intensivo de Redes Cisco (10 Días) + Laboratorio Completo Packet Tracer

## OBJETIVO Y METODOLOGÍA
**NOTA EXCLUSIVA DEL PLAN:** *Este plan será guiado, evaluado y acompañado por un Tutor Virtual de Inteligencia Artificial que indicará el paso a paso desde 0. Él mantendrá un registro en el archivo "Avance.md" a medida que se ejecute el comando "consolida avances".*
**REGLA DE INTERACCIÓN:** *El tutor responderá siempre de manera breve, directa y con los comandos necesarios para ahorrar tokens. Sólo proveerá explicaciones si el usuario lo pide explícitamente.*

Preparación práctica intensiva para rol de redes y ciberseguridad:
- VLANs, STP, EtherChannel
- Routing estático y OSPF
- Inter-VLAN Routing
- ACLs
- NAT/PAT
- Seguridad básica Cisco IOS
- Troubleshooting tipo NOC

---

# 🔥 LAB FINAL COMPLETO (Packet Tracer)

## Modelos de Equipamiento Oficiales
- **Routers (ISP, R1, R2, R3):** ISR 4331 *(Nota: R1 HQ requiere añadir tarjeta Gigabit en pestaña física, ej: NIM-2GE-W-M)*.
- **Simulación de Internet:** 1 Servidor (`Server-PT`) colgado del Router ISP.
- **Switch L3 (Core HQ):** 3650-24PS *(Requiere insertar fuente AC Power en pestaña física)* o 3560-24PS.
- **Switches L2 (Acceso):** 2960-24TT.
- **Terminales:** PC-PT puro.

## Distribución de Terminales / PCs (Total: 14)
*Para garantizar pruebas full de alcance de capa 2/3 (Trunking/EtherChannel).*
- **HQ (6 PCs):** 3 PCs colgando del SW L2-1, y 3 PCs del SW L2-2. (Asignadas 1 a VLAN 10, 1 a VLAN 20, 1 a VLAN 30 por switch).
- **Sucursal B - Cascada (6 PCs):** 3 PCs al SW B1, 3 PCs al SW B2. (Asignadas 1 a VLAN 10, 1 a VLAN 20, 1 a VLAN 30 por switch).
- **Sucursal C (2 PCs):** Ambas al SW L2 (Una VLAN 10 y otra VLAN 20 - para pruebas básicas).
*(Nota: La VLAN 99 no lleva PCs físicas, es exclusiva de SVI de los Switches).*

## Topología Empresarial de 3 Sucursales (Mejorada para STP)

```text
            INTERNET (simulado)
                   |
              [Router ISP]
                   |
              ----------------
              |              |
        (WAN) R1 HQ      (WAN) R2 B
              |              |
           Switch L3      (ROAS) R2
              |              |
           ---|---        Switch L2 (B1)
          |       |          ||  (L2 EtherChannel)
       SW L2    SW L2     Switch L2 (B2)
              |
        (WAN) R3 C (Sucursal C)
              |
           Switch L2
```

---

# 🏢 SUCURSALES

## HQ (Sucursal A - CORE)
- Router R1
- Switch L3 (Core) + 2 Switches L2 (Triángulo STP)
- OSPF + Inter-VLAN (SVI) + NAT

## Sucursal B
- Router R2 
- 2 Switches L2 (Para práctica STP y EtherChannel)
- Inter-VLAN (ROAS)
- Routing estático hacia HQ

## Sucursal C
- Router R3
- 1 Switch L2
- Inter-VLAN (ROAS)
- Red simple + fallback

---

# 📌 VLANs ESTÁNDAR

| VLAN | Nombre        | Red              |
|------|--------------|------------------|
| 10   | USERS        | 192.168.10.0/24  |
| 20   | ADMIN        | 192.168.20.0/24  |
| 30   | IT           | 192.168.30.0/24  |
| 99   | MGMT         | 192.168.99.0/24  |

---

# 🌐 WAN LINKS

- R1 ↔ ISP: 10.0.0.0/30
- R2 ↔ R1: 10.0.1.0/30
- R3 ↔ R1: 10.0.2.0/30

---

# 🧠 DÍA 1-10 RESUMIDO

## DÍA 1: IOS + SSH
- Hostname
- Enable secret
- SSH
- Users local

## DÍA 2: VLAN + Switch
- VLAN creation
- Access / trunk

## DÍA 3: Inter-VLAN
- Router-on-stick o SVI

## DÍA 4: STP + EtherChannel

## DÍA 5: Routing estático

## DÍA 6: OSPF

## DÍA 7: NAT/PAT

## DÍA 8: ACLs

## DÍA 9: Seguridad IOS

## DÍA 10: Troubleshooting

---

# ⚙️ CONFIGURACIÓN COMPLETA

# 🔹 SWITCH L3 (HQ)
```
vlan 10
vlan 20
vlan 30
vlan 99

interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

ip routing
```

---

# 🔹 SWITCH L2 (ejemplo Sucursal B)
```
vlan 10
vlan 20
vlan 30

interface fa0/1
 switchport mode access
 switchport access vlan 10

interface fa0/24
 switchport mode trunk
```

---

# 🔹 ROUTER R1 (HQ CORE)

## Interfaces WAN
```
interface g0/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown
```

## OSPF
```
router ospf 1
 network 192.168.0.0 0.0.255.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
```

---

## NAT
```
access-list 1 permit 192.168.0.0 0.0.255.255
ip nat inside source list 1 interface g0/0 overload
```

---

## ACL SEGURIDAD
```
access-list 100 deny ip 192.168.20.0 0.0.0.255 any
access-list 100 permit ip any any

interface g0/0
 ip access-group 100 in
```

---

# 🔹 ROUTER R2 (Sucursal B)
```
interface g0/0
 ip address 10.0.1.2 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.0.1.1
```

---

# 🔹 ROUTER R3 (Sucursal C)
```
interface g0/0
 ip address 10.0.2.2 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.0.2.1
```

---

# 🔐 SEGURIDAD BASE
```
enable secret cisco123
username admin secret admin123
line vty 0 4
 login local
 transport input ssh
```

---

# 🔁 STP (concepto)
- HQ será root bridge
- Evitar loops

---

# ⚡ ETHERCHANNEL
```
interface range fa0/1-2
 channel-group 1 mode active
```

---

# 🧪 TROUBLESHOOTING (ENTREVISTA)

## Caso 1: VLAN no comunica
- revisar trunk
- revisar VLAN database

## Caso 2: No hay ping entre sucursales
- revisar rutas estáticas / OSPF

## Caso 3: Internet no funciona
- revisar NAT overload

---

# 🎯 RESULTADO FINAL
Al terminar este laboratorio podrás:
- Diseñar red empresarial real
- Configurar switching completo
- Implementar routing híbrido
- Aplicar seguridad básica
- Resolver fallas como NOC L1/L2

---

# 🚀 SIGUIENTE NIVEL (opcional)
Si quieres avanzar aún más:
- Firewall tipo ASA
- Zonas DMZ
- Syslog + monitoreo
- Simulación ataque básico + mitigación

