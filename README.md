# Chufani Network Configuration — Equipo 3

> **Proyecto académico** | Interconexión de Dispositivos · Tec de Monterrey Campus Querétaro
> **Socio Formador:** Grupo Chufani (Constructora, Querétaro, México)

---

## Descripción

Este repositorio contiene los archivos de configuración Cisco IOS para la infraestructura de red corporativa diseñada para **Grupo Chufani**, una empresa constructora ubicada en Querétaro. El proyecto fue desarrollado como parte de la materia *Interconexión de Dispositivos* del Tecnológico de Monterrey, en colaboración directa con ingenieros de la empresa.

La propuesta cubre el diseño lógico completo de la red, el esquema de direccionamiento IP mediante VLSM, y la configuración lista para aplicar en los equipos de interconexión (router, switches y terminales especializadas) dentro de las instalaciones de Chufani.

El repositorio está organizado en **dos esquemas de direccionamiento**:

- **`Sin Escalamiento`** — Subnetting ajustado a la cantidad de hosts actuales en cada área.
- **`Con Escalamiento`** — Subnetting con capacidad de host ampliada para soportar crecimiento futuro.

---

## Equipo

| Nombre                           | Matrícula |
| -------------------------------- | --------- |
| Alexis Yaocalli Berthou Haas     | A01713458 |
| Ana Camila Cuevas González       | A01412609 |
| Ana Sofía Moreno Hernández       | A01707156 |
| Ana Valeria Machuca Miranda      | A01713851 |
| Rodrigo Alejandro Hurtado Cortés | A01713854 |

**Supervisión académica:** Prof. José Oscar Hernández Pérez
**Contacto en empresa:** Ing. Luis Manuel López Beltrán

---

## Estructura del repositorio

```
Chufani_Network_Configuration_Team3/
│
├── Sin Escalamiento/
│   ├── Router/
│   │   └── RF-Chufani.ios                # Router principal (inter-VLAN routing + WAN dual ISP)
│   ├── Switches/
│   │   ├── S-Core.ios
│   │   ├── S-Vigilancia/
│   │   ├── S-CCTV/
│   │   ├── S-AccessControl/
│   │   ├── S-Direccion&CEO/
│   │   ├── S-Diseno/
│   │   ├── S-Estacionamiento/
│   │   ├── S-Oficina&Impresora&Recepcion&ServidoresP3/
│   │   ├── S-Oficina&Impresora&RecepcionP4/
│   │   ├── S-Remoto/
│   │   └── S-Ventas&Presupuestos/
│   └── Terminals/
│       ├── Controlador AC.ios
│       └── Controlador CCTV1.ios
│
├── Con Escalamiento/
│   ├── Router/
│   │   └── RF-Chufani.ios                # Router principal con subnetting escalado
│   ├── Switches/
│   │   ├── S-Core.ios
│   │   ├── S-Vigilancia/
│   │   ├── S-CCTV/
│   │   ├── S-AccessControl/
│   │   ├── S-Direccion&CEO/
│   │   ├── S-Diseno/
│   │   ├── S-Estacionamiento/
│   │   ├── S-Oficina&Impresora&Recepcion&ServidoresP3/
│   │   ├── S-Oficina&Impresora&RecepcionP4/
│   │   ├── S-Remoto/
│   │   └── S-Ventas&Presupuestos/
│   └── Terminals/
│       ├── Controlador AC.ios
│       └── Controlador CCTV1.ios
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## Diseño de red

Ambas versiones utilizan el bloque `172.19.0.0/16` con **16 VLANs** y enrutamiento inter-VLAN mediante **Router-on-a-Stick** (dot1Q) en el router principal RF-Chufani. La diferencia entre versiones radica en el tamaño de subred asignado a cada VLAN.

### VLANs — Sin Escalamiento

Subnetting ajustado al número actual de hosts por área.

| VLAN | Nombre            | Hosts | Prefijo | Dirección IP    | Máscara           | Primera IP    | Última IP      | Broadcast      | DHCP |
| ---- | ----------------- | ----: | ------- | --------------- | ----------------- | ------------- | -------------- | -------------- | :--: |
| 10   | Servidores        | 3     | /29     | 172.19.3.0      | 255.255.255.248   | 172.19.3.1    | 172.19.3.6     | 172.19.3.7     | ❌   |
| 20   | Ventas            | 26    | /27     | 172.19.2.128    | 255.255.255.224   | 172.19.2.129  | 172.19.2.158   | 172.19.2.159   | ✅   |
| 21   | Presupuestos      | 38    | /26     | 172.19.2.0      | 255.255.255.192   | 172.19.2.1    | 172.19.2.62    | 172.19.2.63    | ✅   |
| 30   | Proyectos         | 75    | /25     | 172.19.0.0      | 255.255.255.128   | 172.19.0.1    | 172.19.0.126   | 172.19.0.127   | ✅   |
| 40   | Oficinas          | 112   | /25     | 172.19.0.128    | 255.255.255.128   | 172.19.0.129  | 172.19.0.254   | 172.19.0.255   | ✅   |
| 50   | Dirección         | 20    | /27     | 172.19.2.160    | 255.255.255.224   | 172.19.2.161  | 172.19.2.190   | 172.19.2.191   | ✅   |
| 51   | CEO               | 5     | /29     | 172.19.3.8      | 255.255.255.248   | 172.19.3.9    | 172.19.3.14    | 172.19.3.15    | ✅   |
| 60   | Front Desk        | 8     | /28     | 172.19.2.224    | 255.255.255.240   | 172.19.2.225  | 172.19.2.238   | 172.19.2.239   | ✅   |
| 61   | Invitados         | 50    | /26     | 172.19.2.64     | 255.255.255.192   | 172.19.2.65   | 172.19.2.126   | 172.19.2.127   | ✅   |
| 70   | VozIP             | 88    | /25     | 172.19.1.0      | 255.255.255.128   | 172.19.1.1    | 172.19.1.126   | 172.19.1.127   | ✅   |
| 71   | Impresoras        | 7     | /28     | 172.19.2.240    | 255.255.255.240   | 172.19.2.241  | 172.19.2.254   | 172.19.2.255   | ❌   |
| 80   | CCTV              | 4     | /29     | 172.19.3.16     | 255.255.255.248   | 172.19.3.17   | 172.19.3.22    | 172.19.3.23    | ❌   |
| 81   | Control de Acceso | 4     | /29     | 172.19.3.24     | 255.255.255.248   | 172.19.3.25   | 172.19.3.30    | 172.19.3.31    | ❌   |
| 90   | IoT               | 100   | /25     | 172.19.1.128    | 255.255.255.128   | 172.19.1.129  | 172.19.1.254   | 172.19.1.255   | ❌   |
| 98   | Firewall (Sophos) | 4     | /29     | 172.19.3.32     | 255.255.255.248   | 172.19.3.33   | 172.19.3.38    | 172.19.3.39    | ❌   |
| 911  | Gestión           | 22    | /27     | 172.19.2.192    | 255.255.255.224   | 172.19.2.193  | 172.19.2.222   | 172.19.2.223   | ❌   |

---

### VLANs — Con Escalamiento

Subnetting con capacidad ampliada para contemplar crecimiento futuro de hosts en cada área.

| VLAN | Nombre            | Hosts | Prefijo | Dirección IP    | Máscara           | Primera IP      | Última IP       | Broadcast       | DHCP |
| ---- | ----------------- | ----: | ------- | --------------- | ----------------- | --------------- | --------------- | --------------- | :--: |
| 10   | Servidores        | 9     | /28     | 172.19.11.0     | 255.255.255.240   | 172.19.11.1     | 172.19.11.14    | 172.19.11.15    | ❌   |
| 20   | Ventas            | 78    | /25     | 172.19.9.0      | 255.255.255.128   | 172.19.9.1      | 172.19.9.126    | 172.19.9.127    | ✅   |
| 21   | Presupuestos      | 114   | /25     | 172.19.9.128    | 255.255.255.128   | 172.19.9.129    | 172.19.9.254    | 172.19.9.255    | ✅   |
| 30   | Proyectos         | 225   | /24     | 172.19.8.0      | 255.255.255.0     | 172.19.8.1      | 172.19.8.254    | 172.19.8.255    | ✅   |
| 40   | Oficinas          | 336   | /23     | 172.19.0.0      | 255.255.254.0     | 172.19.0.1      | 172.19.1.254    | 172.19.1.255    | ✅   |
| 50   | Dirección         | 60    | /26     | 172.19.10.0     | 255.255.255.192   | 172.19.10.1     | 172.19.10.62    | 172.19.10.63    | ✅   |
| 51   | CEO               | 15    | /27     | 172.19.10.192   | 255.255.255.224   | 172.19.10.193   | 172.19.10.222   | 172.19.10.223   | ✅   |
| 60   | Front Desk        | 50    | /26     | 172.19.10.64    | 255.255.255.192   | 172.19.10.65    | 172.19.10.126   | 172.19.10.127   | ✅   |
| 61   | Invitados         | 300   | /23     | 172.19.2.0      | 255.255.254.0     | 172.19.2.1      | 172.19.3.254    | 172.19.3.255    | ✅   |
| 70   | VozIP             | 264   | /23     | 172.19.4.0      | 255.255.254.0     | 172.19.4.1      | 172.19.5.254    | 172.19.5.255    | ✅   |
| 71   | Impresoras        | 21    | /27     | 172.19.10.224   | 255.255.255.224   | 172.19.10.225   | 172.19.10.254   | 172.19.10.255   | ❌   |
| 80   | CCTV              | 12    | /28     | 172.19.11.16    | 255.255.255.240   | 172.19.11.17    | 172.19.11.30    | 172.19.11.31    | ❌   |
| 81   | Control de Acceso | 12    | /28     | 172.19.11.32    | 255.255.255.240   | 172.19.11.33    | 172.19.11.46    | 172.19.11.47    | ❌   |
| 90   | IoT               | 300   | /23     | 172.19.6.0      | 255.255.254.0     | 172.19.6.1      | 172.19.7.254    | 172.19.7.255    | ❌   |
| 98   | Firewall (Sophos) | 4     | /29     | 172.19.11.48    | 255.255.255.248   | 172.19.11.49    | 172.19.11.54    | 172.19.11.55    | ❌   |
| 911  | Gestión           | 60    | /26     | 172.19.10.128   | 255.255.255.192   | 172.19.10.129   | 172.19.10.190   | 172.19.10.191   | ❌   |

---

### Diferencias clave entre esquemas

| VLAN | Nombre       | Hosts Sin Esc. | Prefijo Sin Esc. | Hosts Con Esc. | Prefijo Con Esc. |
| ---- | ------------ | -------------: | ---------------- | -------------: | ---------------- |
| 10   | Servidores   | 3              | /29              | 9              | /28              |
| 20   | Ventas       | 26             | /27              | 78             | /25              |
| 21   | Presupuestos | 38             | /26              | 114            | /25              |
| 30   | Proyectos    | 75             | /25              | 225            | /24              |
| 40   | Oficinas     | 112            | /25              | 336            | /23              |
| 50   | Dirección    | 20             | /27              | 60             | /26              |
| 51   | CEO          | 5              | /29              | 15             | /27              |
| 60   | Front Desk   | 8              | /28              | 50             | /26              |
| 61   | Invitados    | 50             | /26              | 300            | /23              |
| 70   | VozIP        | 88             | /25              | 264            | /23              |
| 71   | Impresoras   | 7              | /28              | 21             | /27              |
| 80   | CCTV         | 4              | /29              | 12             | /28              |
| 81   | Ctrl Acceso  | 4              | /29              | 12             | /28              |
| 90   | IoT          | 100            | /25              | 300            | /23              |
| 98   | Firewall     | 4              | /29              | 4              | /29              |
| 911  | Gestión      | 22             | /27              | 60             | /26              |

> El esquema **Con Escalamiento** multiplica aproximadamente **3x** la capacidad de hosts en la mayoría de las VLANs, anticipando crecimiento en cada área. El bloque de direccionamiento base sigue siendo `172.19.0.0/16`.

---

### Router principal — RF-Chufani

- **G0/0/0** → ISP Primario (`192.168.30.254/30`)
- **S0/1/0** → ISP Secundario (`172.16.255.246/30`)
- **G0/0/1.x** → Subinterfaces para routing inter-VLAN (Router-on-a-Stick, dot1Q)

---

### Seguridad global aplicada a todos los dispositivos

- Acceso remoto exclusivamente por **SSH** (RSA 1024 bits)
- Usuarios locales con privilegio 15: `CIT` y `CEO`
- Bloqueo de sesión tras 2 intentos fallidos en 30 segundos (`login block-for`)
- `service password-encryption` habilitado
- `no ip domain-lookup` para evitar retardos por resolución DNS
- Banner MOTD con aviso legal de propiedad de Chufani

---

## Contexto del proyecto

Este proyecto corresponde a la **Fase 05** del diseño de red para Grupo Chufani, que cubre la configuración de los equipos de interconexión a partir del diseño lógico elaborado en fases anteriores.

Se presentan **dos variantes de direccionamiento** sobre el bloque `172.19.0.0/16`:

- **Sin Escalamiento** — definido en la Fase 04, en acuerdo con el ingeniero de la empresa tras la visita a las instalaciones del 26 de mayo de 2026. Refleja el número real de conexiones actuales, dado que el crecimiento inalámbrico futuro será manejado por nuevos Access Points sin impacto en el subnetting cableado.
- **Con Escalamiento** — variante que amplía la capacidad de cada subred a aproximadamente 3x los hosts actuales, contemplando escenarios de expansión de personal y dispositivos por área.

Ambas versiones implementan **VLSM** y mantienen idéntica topología lógica, seguridad y políticas de DHCP.

---

## Licencia

Consultar el archivo `LICENSE` incluido en este repositorio.