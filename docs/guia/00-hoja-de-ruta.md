# Hoja de ruta del proyecto: Lab VMs + VirtualBox + Vagrant + Ansible

> Guía didáctica paso a paso para público sin experiencia previa.  
> Objetivo del proyecto: crear un laboratorio reproducible de 3 a 5 máquinas virtuales en VirtualBox, gestionadas con Vagrant y configuradas con Ansible.

---

## Hoja de ruta del proyecto (qué haremos y en qué orden)

### Fase 0 — Preparación del “terreno” (host)

**Objetivo:** que tu PC esté listo para ejecutar VMs sin problemas.

- Instalar/confirmar: VirtualBox, Vagrant, (opcional) Git, editor (VS Code).
- Activar virtualización en BIOS/UEFI (VT-x / AMD-V).
- Definir carpeta del laboratorio (para que todo quede ordenado y reproducible).

**Checkpoint:** `vagrant --version` y `VBoxManage --version` funcionan en el host.

---

### Fase 1 — Diseño del laboratorio (simple y entendible)

**Objetivo:** saber qué máquinas existen y para qué.

- Elegimos 3–5 VMs con roles claros (ejemplo típico):
  - **control** (Ansible controller)
  - **web** (servidor web)
  - **db** (base de datos)
  - *(opcional)* **monitor** (monitorización)
  - *(opcional)* **client** (cliente de pruebas)

- Decidimos redes:
  - **NAT** para que las VMs salgan a Internet (instalar paquetes).
  - **Host-Only** para que tu PC (host) hable con las VMs y las VMs entre sí en una red privada estable.

**Checkpoint:** tabla con nombres, IPs, RAM/CPU, y red elegida.

---

### Fase 2 — Provisionado con Vagrant + VirtualBox

**Objetivo:** levantar el “esqueleto” de las VMs de forma automática.

- Creamos `Vagrantfile` con:
  - Boxes (Debian/Rocky)
  - IPs privadas (Host-Only)
  - Recursos (RAM/CPU)
  - SSH listo

- Comandos base:
  - `vagrant up`
  - `vagrant status`
  - `vagrant ssh`
  - `vagrant halt`
  - `vagrant destroy`
  - `vagrant reload`

**Checkpoint:** puedes hacer `vagrant ssh web` y ver la IP correcta dentro.

---

### Fase 3 — Automatización con Ansible (configurar lo importante)

**Objetivo:** configurar servicios sin hacerlo “a mano”.

- Inventario (hosts y grupos)
- Playbooks por etapas:
  1. **bootstrap** (usuarios, sudo, claves, Python si hace falta)
  2. **config** (paquetes, servicios)
  3. **verify** (comprobaciones)
- Idempotencia: ejecutar 2 veces y que no “rompa” nada.

**Checkpoint:** `ansible -m ping all` responde OK y un playbook termina sin fallos.

---

### Fase 4 — Validación (evidencias)

**Objetivo:** demostrar que funciona.

- Comandos guiados para validar red, SSH, servicios, puertos.
- Evidencias (capturas y logs) organizadas por paso.

**Checkpoint:** pruebas finales (por ejemplo: web responde, db está arriba, y Ansible reporta todo OK).

---

### Fase 5 — Troubleshooting + Hardening ligero (opcional pero recomendado)

**Objetivo:** saber qué hacer si algo falla y aplicar buenas prácticas.

- Tabla “error → causa probable → solución”
- Seguridad mínima viable:
  - actualizaciones
  - SSH por clave
  - firewall básico (ufw/firewalld)
  - no exponer servicios innecesarios

**Checkpoint:** checklist de diagnóstico rápido + baseline aplicado si se activa.

---

## Cosas que debes entender antes de empezar (explicado sencillo)

### 1) ¿Qué es VirtualBox?
Es el programa que “simula ordenadores” dentro de tu PC. Cada VM es como un mini-PC con su propio disco, RAM, red, etc.

### 2) ¿Qué es Vagrant?
Es el “botón de encendido inteligente” para VMs: con un archivo (`Vagrantfile`) describes cómo deben ser las máquinas y Vagrant las crea igual siempre.

### 3) ¿Qué es Ansible?
Es el “robot de configuración”: en vez de entrar a cada VM a instalar y configurar cosas manualmente, Ansible ejecuta instrucciones declarativas para dejar todo como queremos.

### 4) ¿Por qué usamos NAT + Host-Only?
- **NAT**: las VMs pueden salir a Internet (descargar paquetes), sin exponerlas directamente.
- **Host-Only**: red privada estable para que:
  - tu host (Windows/Linux) se conecte a las VMs
  - Ansible controle las VMs siempre por la misma IP

*(Traducción para novatos: NAT = “salgo fuera”; Host-Only = “hablo dentro del laboratorio”.)*

---

## Requisitos previos recomendados (sin volverse loco)

### En tu host (PC principal)
- **VirtualBox** instalado
- **Vagrant** instalado
- **Git** (opcional, pero ayuda)
- **Terminal**: PowerShell / Windows Terminal / Bash
- **Recursos** sugeridos (para 3 VMs):
  - CPU: 4 cores o más (mejor 6–8)
  - RAM: 16 GB ideal (8 GB puede ir justo)
  - Disco: 30–60 GB libres (según número de VMs)

### En BIOS/UEFI
- Activar **Intel VT-x** o **AMD-V**
- Si hay problemas raros de rendimiento/arranque, revisar Hyper-V (en Windows) según el caso.

---

## Convenciones del laboratorio (para evitar caos)

Estas reglas hacen que el lab sea “repetible” y fácil de depurar:

- **Nombres de VMs** claros: `control`, `web`, `db`, `monitor`, `client`
- **IPs fijas** en Host-Only (ejemplo):
  - control: `192.168.56.10`
  - web: `192.168.56.11`
  - db: `192.168.56.12`

- **Carpeta única** del proyecto (todo dentro):
  - `Vagrantfile`
  - `ansible/`
  - `docs/` (guía + evidencias)
  - `evidencias/` (capturas, logs)

---

## Qué NO haremos al principio (para no liarnos)

- Kubernetes, clusters raros, balanceos complejos
- Demasiados servicios a la vez
- Redes avanzadas si aún no dominamos NAT/Host-Only

Primero lo hacemos **simple, estable y didáctico**. Luego se amplía.

---

## Glosario mínimo (para que no te pierdas)

- **VM**: máquina virtual.
- **Box**: “plantilla” de VM lista para Vagrant (Debian, Rocky…).
- **Provisioning**: crear/configurar automáticamente.
- **Inventario (Ansible)**: lista de máquinas y grupos.
- **Playbook (Ansible)**: recetas de configuración.
- **Idempotencia**: ejecutar varias veces sin romper nada.

---

## Checklist “antes de empezar” (2 minutos)

- [ ] VirtualBox instalado y abre
- [ ] Vagrant instalado: `vagrant --version`
- [ ] Virtualización activada (BIOS/UEFI)
- [ ] Carpeta del proyecto creada
- [ ] Decidido: 3 VMs (control/web/db) para el primer objetivo
