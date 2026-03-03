# (Opcional) Entrega final — Checklist y evidencias del laboratorio

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Objetivo: tener un documento único para **entregar** (clase/portfolio) con:
> - qué debe funcionar
> - qué evidencias adjuntar
> - cómo validar rápido (sin leer toda la guía)

---

## 1) Qué se considera “entregado”

Tu entrega está completa si cumples **dos cosas**:

1) El laboratorio funciona (checklist de funcionamiento)  
2) Aportas evidencias mínimas (capturas o logs)

---

## 2) Checklist de funcionamiento (lo mínimo imprescindible)

Marca todo como ✅ antes de entregar.

### 2.1 HOST (tu PC)
- [ ] VirtualBox instalado y abre sin errores
- [ ] `VBoxManage --version` funciona
- [ ] Vagrant instalado
- [ ] `vagrant --version` funciona
- [ ] Carpeta del proyecto creada (ordenada)

### 2.2 Vagrant / VirtualBox (infra)
- [ ] `vagrant status` muestra `control`, `web`, `db` en `running`
- [ ] `vagrant ssh control` funciona
- [ ] IPs Host-Only son correctas:
  - [ ] `control` → `192.168.56.10`
  - [ ] `web` → `192.168.56.11`
  - [ ] `db` → `192.168.56.12`
- [ ] Ping interno funciona (desde `control`):
  - [ ] `ping -c 2 192.168.56.11` OK
  - [ ] `ping -c 2 192.168.56.12` OK
- [ ] Internet por NAT funciona (en `control`):
  - [ ] `ping -c 2 1.1.1.1` OK

### 2.3 Ansible (automatización)
- [ ] En `control`, `ansible --version` funciona
- [ ] `ansible -m ping lab` → SUCCESS en web y db
- [ ] `ansible-playbook site.yml` termina sin fallos
- [ ] Segunda ejecución de `ansible-playbook site.yml` muestra pocos `changed` (idempotencia)

### 2.4 Servicios (resultado del lab)
- [ ] WEB:
  - [ ] `curl -I http://192.168.56.11` devuelve `HTTP/1.1 200 OK` (o similar)
  - [ ] `curl -s http://192.168.56.11 | grep -i "OK - web configurada por Ansible"` encuentra el texto
- [ ] DB:
  - [ ] En `db`, `systemctl is-active mariadb` devuelve `active`

---

## 3) Evidencias mínimas a adjuntar (recomendado)

> Si te piden pruebas, aquí están las mínimas.  
> Puedes entregar capturas (PNG) o salidas en `.txt`.

### 3.1 Estructura recomendada
```
docs/evidencias/
├─ fase0_host/
├─ fase2_vagrant/
├─ fase3_ansible/
├─ fase4_validacion/
└─ fase5_hardening/   (si lo aplicaste)
```

---

## 4) Evidencias por bloque (lista exacta)

### 4.1 Evidencias del HOST (Fase 0)
Guardar en `docs/evidencias/fase0_host/`:

- `01_versions.txt` con:
  - `vagrant --version`
  - `VBoxManage --version`
  - (opcional) `git --version`

---

### 4.2 Evidencias de Vagrant (Fase 2)
Guardar en `docs/evidencias/fase2_vagrant/`:

- `01_vagrant_status.txt` → `vagrant status`
- `02_control_ip_a.txt` → `ip a` en control
- `03_web_ip_a.txt` → `ip a` en web
- `04_db_ip_a.txt` → `ip a` en db
- `05_ping_control_to_web_db.txt` → pings desde control a web y db
- `06_ping_internet_control.txt` → `ping -c 2 1.1.1.1`

---

### 4.3 Evidencias de Ansible (Fase 3)
Guardar en `docs/evidencias/fase3_ansible/`:

- `01_ansible_version.txt` → `ansible --version`
- `02_ansible_ping_lab.txt` → `ansible -m ping lab`
- `03_playbook_first_run.txt` → `ansible-playbook site.yml` (1ª)
- `04_playbook_second_run.txt` → `ansible-playbook site.yml` (2ª)

---

### 4.4 Evidencias de validación de servicios (Fase 4)
Guardar en `docs/evidencias/fase4_validacion/`:

- `01_curl_web_headers.txt` → `curl -I http://192.168.56.11`
- `02_curl_web_content.txt` → `curl -s http://192.168.56.11 | head`
- `03_db_mariadb_active.txt` → `systemctl is-active mariadb`
- (opcional) `04_db_port_3306.txt` → `ss -lnt | grep 3306`

---

### 4.5 Evidencias de hardening (Fase 5) — si lo aplicaste
Guardar en `docs/evidencias/fase5_hardening/`:

- `01_ufw_status_web.txt` → `ufw status verbose` (en web)
- `02_firewalld_db.txt` → `firewall-cmd --list-all` (en db)
- `03_listening_ports.txt` → `ss -lntp` (en cada VM o al menos en web/db)

---

## 5) Validación rápida “en 2 minutos” (para la entrega)

En el host:

```bash
vagrant status
```

En `control`:

```bash
vagrant ssh control
cd /vagrant/ansible
ansible -m ping lab
ansible-playbook site.yml
curl -I http://192.168.56.11 | head -n 5
ping -c 2 1.1.1.1
```

En `db`:

```bash
vagrant ssh db -c "systemctl is-active mariadb"
```

✅ Si todo esto va bien, el lab está listo para entregar.

---

## 6) Errores típicos en la entrega (y cómo evitarlos)

- **IPs cambiadas**: si cambiaste `192.168.56.X`, actualiza guía + Vagrantfile + inventario.
- **No hay evidencias**: aunque funcione, sin pruebas parece “incompleto”.
- **Ansible no idempotente**: ejecuta dos veces y revisa `changed`.
- **Firewall rompe el acceso**: si activas firewall, asegúrate de permitir 22 y 80.

---

## 7) Qué entregar exactamente (paquete final)

- `README.md`
- `docs/guia/` (todos los md de la guía en orden)
- `docs/evidencias/` (si te piden pruebas)

Opcional:
- el proyecto completo con `Vagrantfile` y `ansible/` para que otros lo puedan reproducir.

---

## 8) Firma (opcional)
Si es para clase/portfolio, añade al final:
- tu nombre
- fecha
- versión del lab

Ejemplo:

- **Autor:** Angel  
- **Fecha:** YYYY-MM-DD  
- **Versión del lab:** 1.0
