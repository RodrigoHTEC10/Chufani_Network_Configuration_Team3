# Chufani Network Configuration — Equipo 3

> **Proyecto académico** | Interconexión de Dispositivos · Tec de Monterrey Campus Querétaro  
> **Socio Formador:** Grupo Chufani (Constructora, Querétaro, México)

---

## Descripción

Este repositorio contiene los archivos de configuración Cisco IOS para la infraestructura de red corporativa diseñada para **Grupo Chufani**, una empresa constructora ubicada en Querétaro. El proyecto fue desarrollado como parte de la materia *Interconexión de Dispositivos* del Tecnológico de Monterrey, en colaboración directa con ingenieros de la empresa.

La propuesta cubre el diseño lógico completo de la red, el esquema de direccionamiento IP mediante VLSM, y la configuración lista para aplicar en los equipos de interconexión (router, switches y terminales especializadas) dentro de las instalaciones de Chufani.

---

## Equipo

| Nombre | Matrícula |
|---|---|
| Alexis Yaocalli Berthou Haas | A01713458 |
| Ana Camila Cuevas González | A01412609 |
| Ana Sofía Moreno Hernández | A01707156 |
| Ana Valeria Machuca Miranda | A01713851 |
| Rodrigo Alejandro Hurtado Cortés | A01713854 |

**Supervisión académica:** Prof. José Oscar Hernández Pérez  
**Contacto en empresa:** Ing. Luis Manuel López Beltrán

---

## Estructura del repositorio

```
Chufani_Network_Configuration_Team3/
│
├── configuracion_global.ios          # Plantilla de configuración base para todos los dispositivos
│
├── Router/
│   └── RF-Chufani.ios                # Router principal (inter-VLAN routing + WAN dual ISP)
│
├── Switches/
│   ├── S-Core.ios                    # Switch core (troncal central)
│   ├── S-Vigilancia/                 # Switches de área de vigilancia (CCTV + Control de Acceso)
│   ├── S-CCTV/                       # Switches dedicados a cámaras CCTV
│   ├── S-AccessControl/              # Switches de control de acceso físico
│   ├── S-Direccion&CEO/              # Switches del área directiva
│   ├── S-Diseno/                     # Switches del área de diseño / proyectos
│   ├── S-Estacionamiento/            # Switches del estacionamiento
│   ├── S-Oficina&Impresora&Recepcion&ServidoresP3/   # Switches piso 3 (oficinas, impresoras, recepción, servidores)
│   ├── S-Oficina&Impresora&RecepcionP4/              # Switches piso 4 (oficinas, impresoras, recepción)
│   ├── S-Remoto/                     # Switches de área remota
│   └── S-Ventas&Presupuestos/        # Switches del área comercial
│
└── Terminals/
    ├── Controlador AC.ios            # Configuración del controlador de acceso físico
    └── Controlador CCTV1.ios         # Configuración del controlador CCTV
```

---

## Diseño de red

### VLANs configuradas

| VLAN | Nombre | Subred | Prefijo | DHCP |
|------|--------|--------|---------|------|
| 10 | Servidores | 172.19.3.0 | /29 | ❌ |
| 20 | Ventas | 172.19.2.128 | /27 | ✅ |
| 21 | Presupuestos | 172.19.2.0 | /26 | ✅ |
| 30 | Proyectos | 172.19.0.0 | /25 | ✅ |
| 40 | Oficinas | 172.19.0.128 | /25 | ✅ |
| 50 | Dirección | 172.19.2.160 | /27 | ✅ |
| 51 | CEO | 172.19.3.8 | /29 | ✅ |
| 60 | Front Desk | 172.19.2.224 | /28 | ✅ |
| 61 | Invitados | 172.19.2.64 | /26 | ✅ |
| 70 | VozIP | 172.19.1.0 | /25 | ✅ |
| 71 | Impresoras | 172.19.2.240 | /28 | ❌ |
| 80 | CCTV | 172.19.3.16 | /29 | ❌ |
| 81 | Control de Acceso | 172.19.3.24 | /29 | ❌ |
| 90 | IoT | 172.19.1.128 | /25 | ❌ |
| 98 | Firewall (Sophos) | 172.19.3.32 | /29 | ❌ |
| 911 | Gestión | 172.19.2.192 | /27 | ❌ |

### Router principal — RF-Chufani

- **G0/0/0** → ISP Primario (`192.168.30.254/30`)
- **S0/1/0** → ISP Secundario (`172.16.255.246/30`)
- **G0/0/1.x** → Subinterfaces para routing inter-VLAN (Router-on-a-Stick, dot1Q)

### Seguridad global aplicada a todos los dispositivos

- Acceso remoto exclusivamente por **SSH** (RSA 1024 bits)
- Usuarios locales con privilegio 15: `CIT` y `CEO`
- Bloqueo de sesión tras 2 intentos fallidos en 30 segundos (`login block-for`)
- `service password-encryption` habilitado
- `no ip domain-lookup` para evitar retardos por resolución DNS
- Banner MOTD con aviso legal de propiedad de Chufani

---

## Contexto del proyecto

Este proyecto corresponde a la **Fase 05** del diseño de red para Grupo Chufani, que cubre la configuración de los equipos de interconexión a partir del diseño lógico elaborado en fases anteriores. Se utilizó el esquema de direccionamiento **sin escalamiento** (definido en la Fase 04), en acuerdo con el ingeniero de la empresa tras la visita a las instalaciones el 26 de mayo de 2026, dado que el crecimiento de conexiones físicas no está contemplado y las conexiones inalámbricas futuras serán manejadas por nuevos Access Points.

El direccionamiento se realizó mediante **VLSM** sobre el bloque `172.19.0.0/16`, asignando subredes ajustadas a las necesidades reales de cada área. Las VLANs con pocos hosts usan prefijo `/29` mínimo (3 bits de host) para dejar margen de crecimiento.

---

## Licencia

Consultar el archivo `LICENSE` incluido en este repositorio.