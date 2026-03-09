# Fase 5 — Troubleshooting + Hardening ligero (opcional pero recomendado)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Público objetivo: **personas sin experiencia**  
> Objetivo de esta fase:  
> 1) Tener una guía de **diagnóstico rápido** cuando algo falla.  
> 2) Aplicar un **baseline de seguridad mínimo** para un laboratorio (sin romper el acceso).

---

## 0) Nota importante (para que no te quedes “fuera”)

- Antes de tocar SSH o firewall, confirma que **tu acceso por clave funciona**.
- Aplica cambios **primero en `web`**, valida, y después en el resto.
- Si activas firewall, asegúrate de permitir:
  - **22/tcp** (SSH)
  - **80/tcp** (solo en `web`)
  - **3306/tcp** (solo si necesitas que `web` acceda a la DB por red)

---

## 1) Checklist de diagnóstico rápido (1 minuto)

1) En el host:
```bash
vagrant status
```

2) SSH:
```bash
vagrant ssh control
vagrant ssh web
vagrant ssh db
```

3) IPs:
```bash
ip a
```

4) Ping interno (desde control):
```bash
ping -c 2 192.168.56.11
ping -c 2 192.168.56.12
```

5) Internet (NAT):
```bash
ping -c 2 1.1.1.1
```

6) DNS:
```bash
ping -c 2 deb.debian.org
```

7) Ansible:
```bash
cd /vagrant/ansible
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab
```

8) Servicios:
- Web:
```bash
systemctl status nginx --no-pager | head
curl -I http://localhost | head
```
- DB:
```bash
systemctl status mariadb --no-pager | head
```

---

## 2) Tabla de troubleshooting (error → causa probable → solución)

| Síntoma | Causa probable | Solución |
|---|---|---|
| `vagrant up` falla | Virtualización desactivada | Activar VT-x/AMD-V en BIOS/UEFI |
| VMs lentas en Windows | Conflicto con Hyper-V/WSL2 | Revisar características de Windows y compatibilidad VirtualBox |
| No hay ping entre VMs | Host-Only mal aplicada | `vagrant reload` y revisar `ip a` |
| `ping 1.1.1.1` OK pero dominios no | DNS | revisar `/etc/resolv.conf`, `systemd-resolved` o NetworkManager |
| `ansible` UNREACHABLE | SSH/clave/usuario | probar SSH manual y revisar `inventory.ini` |
| Web no responde | nginx/firewall | `systemctl status nginx` y revisar reglas |
| DB no arranca | servicio/paquete | `systemctl list-unit-files | grep -i maria` |

---

## 3) Hardening ligero (baseline mínimo viable)

### 3.1 Actualizaciones del sistema

Debian (control/web):
```bash
sudo apt update
sudo apt upgrade -y
```

Rocky (db):
```bash
sudo dnf update -y
```

---

### 3.2 SSH (mínimo)
Opciones recomendadas en `/etc/ssh/sshd_config`:

```text
PermitRootLogin no
PasswordAuthentication no
```

Aplicar:
```bash
sudo systemctl restart sshd || sudo systemctl restart ssh
```

> ⚠️ Si desactivas contraseña, confirma antes que tu clave funciona.

---

## 4) Firewall básico (recomendación por defecto: COMANDOS)

> Para un curso, lo más estable es usar **comandos** (sin dependencias extra de Ansible Galaxy).

### 4.1 Debian (UFW)

En `web`:
```bash
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw --force enable
sudo ufw status verbose
```

En `control` (si quieres dejarlo también con firewall):
```bash
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw --force enable
sudo ufw status verbose
```

### 4.2 Rocky (firewalld)

En `db`:
```bash
sudo systemctl enable --now firewalld
sudo firewall-cmd --permanent --add-service=ssh
# Solo si necesitas acceso a DB desde web/control:
sudo firewall-cmd --permanent --add-port=3306/tcp
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

---

## 5) (Opcional) Hardening con Ansible — dos opciones

### Opción A (Ansible “puro” con colecciones)
Requiere instalar colecciones en `control`:
```bash
ansible-galaxy collection install community.general ansible.posix
```

Luego puedes usar módulos como `community.general.ufw` y `ansible.posix.firewalld`.

### Opción B (recomendada para curso: sin colecciones)
Usa `command`/`shell` para firewall (menos elegante, más simple).  
Ejemplo para `web` (UFW):

```yaml
---
- name: Firewall web (UFW sin colecciones)
  hosts: web
  become: true
  tasks:
    - name: Instalar ufw
      ansible.builtin.apt:
        name: ufw
        state: present
        update_cache: yes
    - name: Reglas UFW
      ansible.builtin.command: "{{ item }}"
      loop:
        - ufw default deny incoming
        - ufw default allow outgoing
        - ufw allow 22/tcp
        - ufw allow 80/tcp
        - ufw --force enable
      changed_when: false
```

---

## 6) Evidencias de la Fase 5 (qué guardar)

Guarda en `docs/evidencias/fase5_hardening/`:

- `01_updates_debian.txt`
- `02_updates_rocky.txt`
- `10_ufw_status_web.txt`
- `20_firewalld_status_db.txt`
- `30_ss_listening_ports.txt` (opcional)

Comando útil para puertos:
```bash
sudo ss -lntp
```

---

## 7) Checkpoint final de la Fase 5

- [ ] Sistemas actualizados (Debian y Rocky)
- [ ] SSH reforzado (sin root; contraseña desactivada solo si procede)
- [ ] Firewall activo sin romper nada:
  - [ ] `web` permite 22 y 80
  - [ ] `db` permite 22 (y 3306 si se requiere)
- [ ] `curl http://192.168.56.11` sigue funcionando
- [ ] `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` sigue funcionando
