# (Opcional recomendado) Ajustes de calidad de la guía y del laboratorio

> Objetivo: mejorar la guía y el laboratorio para que sea más **coherente, fácil de seguir, reproducible y “entregable”**.  
> Este documento NO añade nuevas fases, sino que **refina** lo ya creado.

---

## 1) Coherencia de rutas y nombres (evitar confusión)

### 1.1 Regla general
- Todo lo relacionado con Ansible vive en: **`/vagrant/ansible`** (dentro de la VM `control`)  
- Documentación: **`docs/guia/`**  
- Evidencias: **`docs/evidencias/`**

### 1.2 Rutas “oficiales” (recomendadas)
En el host:
- Proyecto: `lab-vagrant-ansible/`
- Guía: `lab-vagrant-ansible/docs/guia/`
- Evidencias: `lab-vagrant-ansible/docs/evidencias/`

En la VM `control`:
- Carpeta compartida: `/vagrant`
- Ansible: `/vagrant/ansible`

✅ Consejo: en todas las fases, cuando digas “entra a ansible”, siempre usa:
```bash
cd /vagrant/ansible
```

---

## 2) Unificar IPs, nombres y redes (SSOT del laboratorio)

### 2.1 SSOT (Single Source of Truth)
Define un único sitio donde vivirán las IPs y nombres:  
- En la guía: `docs/guia/02_fase_1_diseno.md`  
- En el código: `Vagrantfile`

### 2.2 Tabla oficial (no cambiar)
| VM | Rol | Host-Only IP |
|---|---|---|
| control | Ansible controller | 192.168.56.10 |
| web | Servidor web | 192.168.56.11 |
| db | Base de datos | 192.168.56.12 |

### 2.3 “Síntoma de incoherencia”
Si en algún documento aparece:
- otra IP
- otro nombre de VM
- otra red

👉 corrígelo inmediatamente: un lab reproducible se rompe por detalles pequeños.

---

## 3) Windows como host (sección específica recomendada)

> Mucha gente hará este lab en Windows. Añade esta mini-sección en el README o en la Fase 0.

### 3.1 Terminal recomendada
- Windows Terminal + PowerShell
- Git Bash (si lo prefieres)

### 3.2 Problemas típicos
- Conflicto con Hyper-V / WSL2 / Virtual Machine Platform
- Antivirus/seguridad bloquea VirtualBox
- Permisos al crear redes Host-Only

### 3.3 “Cómo reportar un error” (para ayudarte a ti mismo)
Si algo falla, apunta:
- mensaje exacto del error
- salida de:
  - `vagrant version`
  - `VBoxManage --version`
  - `vagrant up --debug` (si hace falta)

---

## 4) Calidad de la automatización: idempotencia y módulos correctos

### 4.1 Regla: evita `shell` si existe módulo
En Ansible:
- instalar paquetes → `apt` / `dnf` / `package`
- servicios → `service` / `systemd`
- copiar archivos → `copy` / `template`
- comprobar HTTP → `uri`

Esto mejora:
- idempotencia
- legibilidad
- portabilidad

### 4.2 “Prueba de idempotencia”
Tu playbook es bueno si:
- primera ejecución: configura (algunos `changed`)
- segunda ejecución: casi todo `ok` (pocos `changed`)

---

## 5) Ajuste importante: colecciones de Ansible (evitar sorpresas)

En la Fase 5 se propuso `community.general.ufw`. Eso puede fallar si no tienes la colección instalada.

Tienes dos caminos:

### Camino A (recomendado si quieres Ansible “limpio”)
Añade un paso para instalar colecciones en `control`:

```bash
ansible-galaxy collection install community.general ansible.posix
```

Y documenta que es un requisito.

### Camino B (recomendado para principiantes, cero dependencias)
No uses módulos de colecciones para firewall y hazlo con comandos.

Ejemplo (Debian web):
```yaml
- name: Configurar UFW (sin colecciones)
  hosts: web
  become: true
  tasks:
    - apt:
        name: ufw
        state: present
        update_cache: yes
    - command: ufw default deny incoming
      changed_when: false
    - command: ufw default allow outgoing
      changed_when: false
    - command: ufw allow 22/tcp
      changed_when: false
    - command: ufw allow 80/tcp
      changed_when: false
    - command: ufw --force enable
      changed_when: false
```

> Nota: con `command` pierdes parte de la idempotencia “perfecta”, pero ganas simplicidad.

✅ Para una guía de novatos, el Camino B suele ser más estable.

---

## 6) Ajuste de Fase 3: clave SSH y consistencia del inventario

### 6.1 Regla: una única clave para el lab
- Genera una clave en `control` (usuario `vagrant`)
- Copia a `web` y `db`
- Usa esa clave en `inventory.ini`

### 6.2 Prueba rápida (antes de Ansible)
```bash
ssh -i /home/vagrant/.ssh/id_ed25519 vagrant@192.168.56.11 "hostname"
ssh -i /home/vagrant/.ssh/id_ed25519 vagrant@192.168.56.12 "hostname"
```

Si SSH falla, Ansible fallará también.

---

## 7) Mejoras de documentación (para que sea “entregable”)

### 7.1 Añadir “Qué aprenderás” al inicio de cada fase
Ejemplo:
- Objetivo
- Qué conseguirás
- Checkpoint

### 7.2 Añadir “Errores comunes” al final de cada fase
2–3 errores comunes por fase ayudan muchísimo a novatos.

### 7.3 Añadir “Resumen en 30 segundos”
Ayuda a repasar antes de exámenes.

---

## 8) Checklist de control de calidad (QA)

Marca esto antes de dar la guía por finalizada:

- [ ] Todas las fases usan IPs y nombres coherentes
- [ ] Todas las rutas son consistentes (`/vagrant/ansible`, `docs/evidencias/`)
- [ ] Fase 3: `ansible -m ping lab` funciona
- [ ] Fase 3: `ansible-playbook site.yml` funciona 2 veces (idempotencia)
- [ ] Fase 4: evidencias guardadas y organizadas
- [ ] Fase 5: hardening no rompe el acceso ni la web
- [ ] README enlaza a todo y el índice funciona

---

## 9) Acciones recomendadas (si quieres que lo dejemos perfecto)

1) Generar un **`00_indice.md`** navegable para `docs/guia/`  
2) Decidir Camino A o B para firewall (colecciones vs comandos)  
3) Añadir una sección “Windows host” en la Fase 0 o README  
4) Hacer una pasada final de coherencia (IPs/rutas)

> Si me dices “elige tú”, elegiré **Camino B** (más simple para novatos) y te genero los cambios como nuevos .md.
