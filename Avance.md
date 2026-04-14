# Registro de Avances - Plan Intensivo Redes Cisco

Este archivo mantendrá un registro consolidado de los temas, configuraciones y laboratorios que hemos completado satisfactoriamente.

## Progreso General

- [x] Topología Base (Día 0)
- [x] Día 1: IOS + SSH
- [ ] Día 2: VLAN + Switch
- [ ] Día 3: Inter-VLAN
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
| `hostname [nombre]` | Asigna nombre de sistema. Requisito obligatorio para activar SSH. |
| `no ip domain-lookup` | Evita que la consola se congele (Translating...) por un error de tipeo. |
| `enable secret [clave]` | Contraseña maestra para modo privilegiado (totalmente cifrada en la configuración). |
| `service password-encryption`| Protege/ofusca cualquier otra contraseña en texto plano en la configuración del equipo. |
| `username [user] privilege 15 secret [pwd]` | Crea usuario administrativo local con máximos privilegios (nivel 15). |
| `ip domain-name [dominio]` | Declara dominio. Necesario (junto al hostname) para la semilla de la llave criptográfica. |
| `crypto key generate rsa` | Genera los certificados RSA para cifrar la conexión SSH (normalmente 1024 o 2048 bits). |
| `line vty 0 4` | Configura el acceso para las 5 terminales virtuales de administración remota de Cisco. |
| `transport input ssh` | Restringe el acceso aceptando SÓLO conexiones seguras SSH (bloquea Telnet). |
| `login local` | Obliga a la terminal remota a usar nuestro usuario/contraseña de la database local. |
| `copy running-config startup-config` | (Abreviado: `wr`) Guarda la configuración de la RAM hacia la NVRAM para no perderla. |
