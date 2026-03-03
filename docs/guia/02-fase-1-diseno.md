# Fase 1 — Diseño del laboratorio (simple y entendible)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Público objetivo: **personas sin experiencia previa**  
> Objetivo de esta fase: **definir qué máquinas vamos a tener, para qué sirve cada una, qué red usaremos y con qué IP/recursos**, antes de escribir el `Vagrantfile`.

---

## 1) Qué vamos a construir (visión rápida)

Un laboratorio típico para aprender Vagrant + Ansible tiene 3 piezas mínimas:

- **control** → donde ejecutas Ansible (el “cerebro”)
- **web** → un servidor con un servicio visible (para validar)
- **db** → un servidor de base de datos (para practicar separación de roles)

Opcionales (cuando ya te sientas cómodo):

- **monitor** → monitorización (Prometheus / node_exporter / etc.)
- **client** → máquina “cliente” para pruebas (curl, dig, etc.)

> Recomendación: **empieza con 3 VMs**. Cuando todo funcione, añadir una 4ª es trivial.

---

## 2) Roles de cada VM (en lenguaje humano)

### 2.1 control (Ansible controller)
**Qué es:** la máquina desde la que lanzas Ansible.  
**Para qué sirve:** centraliza la automatización.

Tareas típicas:
- Tener instalado Ansible
- Guardar el inventario (`inventory.ini`) y playbooks (`site.yml`)
- Acceder por SSH a `web` y `db`

---

### 2.2 web (servidor web)
**Qué es:** un servidor con un servicio fácil de comprobar (HTTP).  
**Para qué sirve:** validar que Ansible instala y configura correctamente.

Tareas típicas:
- Instalar y habilitar un servidor web (nginx o apache)
- Publicar una página simple (index.html)
- Verificar accesos desde el host o desde `client`

---

### 2.3 db (servidor de base de datos)
**Qué es:** servidor dedicado a base de datos.  
**Para qué sirve:** practicar “separación de responsabilidades” (web ≠ db).

Tareas típicas:
- Instalar MariaDB / PostgreSQL
- Crear usuario y base de datos de práctica
- Permitir acceso desde `web` (si lo configuramos)

---

## 3) Elección de sistemas operativos (Debian + RedHat)

Para aprender bien, conviene tocar **dos familias**:

- **Debian/Ubuntu** (APT)
- **RHEL/Rocky/Alma** (DNF)

### Propuesta recomendada (mixta y didáctica)
- **control**: Debian (más ligero y cómodo para Ansible)
- **web**: Debian
- **db**: Rocky Linux (para practicar dnf, firewalld, etc.)

> Alternativa: todo Debian (más simple) o todo Rocky (también válido).  
> Aquí usaremos **mixto** porque el proyecto pide tener en cuenta Debian y RedHat.

---

## 4) Redes: NAT + Host-Only (por qué y cómo)

Para un lab estable, usamos **2 interfaces por VM**:

### 4.1 NAT (salida a Internet)
Sirve para:
- Descargar paquetes (apt/dnf)
- Actualizar el sistema

Características:
- La VM **sale a Internet**
- Tu host normalmente **no entra** a la VM por NAT (no lo necesitamos)

---

### 4.2 Host-Only (red privada del laboratorio)
Sirve para:
- Que el host hable con las VMs por IP fija
- Que Ansible controle siempre por IP estable
- Que `web` y `db` se vean entre sí sin depender de Internet

Características:
- Red **privada**, no sale a Internet (por sí sola)
- IPs **fijas** (ideal para inventario Ansible)

---

## 5) Plan de direccionamiento (IPs fijas)

Usaremos una red Host-Only típica:

- **Red**: `192.168.56.0/24`
- **Gateway**: no hace falta (es laboratorio)
- **Rango**: 192.168.56.10–50 (nos reservamos espacio)

### Tabla de IPs (propuesta)
| VM | Rol | SO | Host-Only IP | NAT |
|---|---|---|---|---|
| control | Ansible controller | Debian | 192.168.56.10 | Sí |
| web | Servidor web | Debian | 192.168.56.11 | Sí |
| db | Base de datos | Rocky Linux | 192.168.56.12 | Sí |
| monitor (opcional) | Monitorización | Debian/Rocky | 192.168.56.13 | Sí |
| client (opcional) | Cliente pruebas | Debian | 192.168.56.14 | Sí |

**Regla de oro:** no cambies estas IPs una vez empieces, porque:
- Vagrant
- Inventario Ansible
- Documentación
… todo se apoya en ellas.

---

## 6) Recursos (CPU/RAM) recomendados

La idea es que el lab funcione en un PC normal sin ir lento.

### Recomendación mínima (3 VMs)
| VM | CPU | RAM | Disco |
|---|---:|---:|---:|
| control | 1 vCPU | 1024–1536 MB | 10–20 GB |
| web | 1 vCPU | 1024 MB | 10–20 GB |
| db | 1–2 vCPU | 1536–2048 MB | 10–20 GB |

> Si tu PC tiene 16 GB RAM, esto suele ir bien.  
> Si tienes 8 GB RAM, baja `db` a 1536 MB y evita añadir `monitor` por ahora.

---

## 7) Nombres y convenciones (para que todo sea ordenado)

### 7.1 Nombres de VMs
Usaremos nombres cortos y claros:
- `control`, `web`, `db` (y opcionales `monitor`, `client`)

### 7.2 Nombres dentro de Linux (hostname)
Coinciden con el nombre:
- `control`, `web`, `db`

### 7.3 Estructura de carpetas del lab
(la crearemos en la Fase 2, pero la dejamos diseñada ya)

```
lab-vagrant-ansible/
├─ Vagrantfile
├─ ansible/
│  ├─ inventory.ini
│  ├─ site.yml
│  ├─ group_vars/
│  ├─ roles/
│  └─ files/
├─ docs/
│  ├─ 01_diseno_laboratorio.md
│  └─ evidencias/
└─ scripts/ (opcional)
```

---

## 8) Qué validaremos al final de la Fase 1 (Checkpoint)

✅ Checklist de diseño (debe quedar “cerrado”):
- [ ] Lista final de VMs (mínimo 3)
- [ ] Rol de cada VM definido (control/web/db)
- [ ] Red elegida: NAT + Host-Only
- [ ] IPs fijas definidas
- [ ] Recursos CPU/RAM definidos
- [ ] Convenciones de nombres y estructura del proyecto definidas

> Cuando todo esto está claro, la **Fase 2** es “simple”: escribir `Vagrantfile` con esta información.

---

## 9) Resultado final de esta fase (resumen en 30 segundos)

Este laboratorio queda definido así:

- **3 VMs**: `control` (Debian), `web` (Debian), `db` (Rocky)
- **2 redes** por VM: NAT (Internet) + Host-Only (IP fija)
- **IPs**: 192.168.56.10 / .11 / .12
- **Objetivo**: levantar con Vagrant y configurar con Ansible de forma reproducible

---

## Próximo archivo del proyecto (Fase 2)
En la Fase 2 crearemos:
- `Vagrantfile` comentado (con estas VMs, IPs y recursos)
- comandos básicos (`vagrant up`, `vagrant ssh`, etc.)
- verificación de IP y conectividad
