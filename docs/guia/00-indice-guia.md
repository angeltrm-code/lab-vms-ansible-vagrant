# Índice de la guía (navegable)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Objetivo: que puedas navegar la guía en orden, saber qué aprenderás en cada fase y qué evidencia guardar.

---

## 0) Cómo usar este índice

1) Lee primero **Conceptos** (Vagrant y Ansible).  
2) Revisa la **Hoja de ruta** para ver la película completa.  
3) Sigue las **Fases 0 → 5** en orden.  
4) Al final, usa el documento **README** como vista general (y/o el checklist de entrega si lo tienes).

> Este índice asume que todos estos `.md` están en la **misma carpeta** (por ejemplo `docs/guia/`).  
> Si los tienes en otra ruta, mantén los nombres y ajusta los enlaces.

---

## 1) Documentos principales (orden recomendado)

### 1.1 Conceptos (lectura previa)
- [00-conceptos-vagrant-ansible.md](00-conceptos-vagrant-ansible.md)  
  *(Qué es VirtualBox, Vagrant y Ansible; cómo encajan entre sí.)*

**Checklist rápido:**
- [ ] Entiendo qué es una VM y qué hace VirtualBox
- [ ] Entiendo qué hace Vagrant (crear/gestionar VMs)
- [ ] Entiendo qué hace Ansible (configurar VMs por SSH)

---

### 1.2 Hoja de ruta
- [00-hoja-de-ruta.md](00-hoja-de-ruta.md)  
  *(Qué haremos y en qué orden: fases 0–5.)*

**Checklist rápido:**
- [ ] Entiendo el objetivo final del laboratorio
- [ ] Entiendo qué se consigue en cada fase

---

## 2) Guía por fases (paso a paso)

### Fase 0 — Preparación del host (tu PC)
- [01-fase-0-host.md](01-fase-0-host.md)

**Qué aprenderás:**
- Preparar VirtualBox + Vagrant
- Verificar virtualización (VT-x/AMD-V)
- Crear la estructura inicial del proyecto

**Checkpoint:**
- `vagrant --version` OK
- `VBoxManage --version` OK

**Evidencias recomendadas:**
- `docs/evidencias/fase0_host/01_versions.txt`

---

### Fase 1 — Diseño del laboratorio
- [02-fase-1-diseno.md](02-fase-1-diseno.md)

**Qué aprenderás:**
- Elegir VMs (control/web/db)
- Definir redes (NAT + Host-Only)
- Definir IPs fijas y recursos (CPU/RAM)

**Checkpoint:**
- Tabla final con nombres, IPs y recursos lista

---

### Fase 2 — Provisionado con Vagrant + VirtualBox
- [03-fase-2-vagrant.md](03-fase-2-vagrant.md)

**Qué aprenderás:**
- Crear el `Vagrantfile`
- Levantar VMs con `vagrant up`
- Entrar por SSH con `vagrant ssh`

**Checkpoint:**
- `vagrant status` → 3 VMs en `running`
- IPs 192.168.56.10/11/12 visibles en `ip a`
- Ping interno funciona

**Evidencias recomendadas:**
- `docs/evidencias/fase2_vagrant/01_vagrant_status.txt`
- `docs/evidencias/fase2_vagrant/*_ip_a.txt`
- `docs/evidencias/fase2_vagrant/05_ping_control_to_web_db.txt`

---

### Fase 3 — Automatización con Ansible
- [04-fase-3-ansible.md](04-fase-3-ansible.md)

**Qué aprenderás:**
- Instalar Ansible en `control`
- Crear inventario
- Ejecutar playbooks por etapas: bootstrap → config → verify
- Entender idempotencia

**Checkpoint:**
- `ansible -m ping lab` → SUCCESS
- `ansible-playbook site.yml` sin fallos
- Segunda ejecución con pocos `changed`

**Evidencias recomendadas:**
- `docs/evidencias/fase3_ansible/02_ansible_ping_lab.txt`
- `docs/evidencias/fase3_ansible/03_playbook_first_run.txt`
- `docs/evidencias/fase3_ansible/04_playbook_second_run.txt`

---

### Fase 4 — Validación (evidencias)
- [05-fase-4-validacion.md](05-fase-4-validacion.md)

**Qué aprenderás:**
- Validar red, SSH, servicios
- Guardar evidencias correctamente

**Checkpoint:**
- `curl http://192.168.56.11` responde
- `systemctl is-active mariadb` → active

**Evidencias recomendadas:**
- `docs/evidencias/fase4_validacion/01_curl_web_headers.txt`
- `docs/evidencias/fase4_validacion/03_db_mariadb_active.txt`

---

### Fase 5 — Troubleshooting + Hardening ligero
- [06-fase-5-hardening.md](06-fase-5-hardening.md)

**Qué aprenderás:**
- Diagnóstico rápido
- Actualizaciones
- Firewall básico
- SSH con buenas prácticas

**Checkpoint:**
- El hardening NO rompe Ansible ni la web

**Evidencias recomendadas:**
- `docs/evidencias/fase5_hardening/01_ufw_status_web.txt`
- `docs/evidencias/fase5_hardening/02_firewalld_db.txt`

---

## 3) Documentos de soporte (vista general)

### README (guía completa + estructura del repo)
- [README.md](../../README.md)

> README principal en la raiz del repo.

---

## 4) Checklist global (cuando termines)

- [ ] Fase 0 completada (host OK)
- [ ] Fase 1 completada (diseño cerrado)
- [ ] Fase 2 completada (VMs arriba)
- [ ] Fase 3 completada (Ansible OK + idempotencia)
- [ ] Fase 4 completada (validación + evidencias)
- [ ] Fase 5 completada (hardening sin romper nada) *(opcional)*

---

## 5) Próximo paso (si quieres ampliar el lab)

- Añadir VM `monitor`
- Convertir playbooks en roles
- Templates Jinja2
- Servicios extra (DNS, reverse proxy, etc.)