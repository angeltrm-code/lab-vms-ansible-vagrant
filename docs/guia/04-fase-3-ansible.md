# Fase 3 — Automatización con Ansible (configurar lo importante)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Público objetivo: **personas sin experiencia**  
> Objetivo de esta fase: usar Ansible desde `control` para **configurar** `web` y `db` de forma **repetible** (idempotente), con un flujo por etapas: **bootstrap → config → verify**.

---

## 0) Qué vas a conseguir al terminar esta fase

✅ Desde `control` podrás ejecutar:

- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` → OK
- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` → instala y configura servicios

✅ Resultado final (ejemplo didáctico):
- En **web**: servidor web instalado y funcionando (nginx por defecto)
- En **db**: MariaDB instalado y arrancado
- Validación: Ansible confirma estado y tú lo verificas con comandos y/o desde el host.

---

## 1) Estructura de carpetas de Ansible (en el host)

En la carpeta del laboratorio (donde está `Vagrantfile`), crea:

```
ansible/
├─ inventory.ini
├─ ansible.cfg
├─ site.yml
├─ playbooks/
│  ├─ 01_bootstrap.yml
│  ├─ 02_config.yml
│  └─ 03_verify.yml
└─ group_vars/
   ├─ all.yml
   ├─ debian.yml
   └─ rocky.yml
```

> Esta estructura ayuda a novatos a “ver” el orden.

---

## 2) Entrar en `control` e instalar Ansible

> Recomendación: ejecutar Ansible **dentro de la VM `control`**.  
> Así tu host puede ser Windows y da igual: el controller será Linux.

En el host:

```bash
vagrant ssh control
```

Dentro de `control` (Debian):

```bash
sudo apt update
sudo apt install -y ansible openssh-client
ansible --version
```

**Checkpoint:** `ansible --version` muestra versión instalada.

---

## 3) Preparar SSH (clave única del laboratorio)

> Vamos a usar **una única clave** del usuario `vagrant` en `control` para entrar a `web` y `db`.

En `control`:

```bash
ssh-keygen -t ed25519 -N "" -f /home/vagrant/.ssh/id_ed25519
```

Copiar la clave pública a `web` y `db`:

```bash
ssh-copy-id -i /home/vagrant/.ssh/id_ed25519.pub vagrant@192.168.56.11
ssh-copy-id -i /home/vagrant/.ssh/id_ed25519.pub vagrant@192.168.56.12
```

**Checkpoint SSH (antes de Ansible):**

```bash
ssh -i /home/vagrant/.ssh/id_ed25519 vagrant@192.168.56.11 "hostname"
ssh -i /home/vagrant/.ssh/id_ed25519 vagrant@192.168.56.12 "hostname"
```

Debe responder `web` y `db`.

---

## 4) Configurar Ansible (`ansible.cfg`) para que sea cómodo

Trabajaremos en `/vagrant/ansible` (carpeta compartida del proyecto).

En `control`:

```bash
cd /vagrant/ansible
```

Crea `ansible.cfg`:

```ini
[defaults]
inventory = inventory.ini
host_key_checking = False
retry_files_enabled = False
stdout_callback = yaml
interpreter_python = auto_silent

[ssh_connection]
pipelining = True
```

> `host_key_checking = False` evita preguntas al conectar (para labs didácticos).

---

## 5) Crear el inventario (`inventory.ini`)

Crea `inventory.ini` en `/vagrant/ansible`:

```ini
[web]
web ansible_host=192.168.56.11

[db]
db ansible_host=192.168.56.12

[debian:children]
web

[rocky:children]
db

[lab:children]
debian
rocky

[lab:vars]
ansible_user=vagrant
ansible_ssh_private_key_file=/home/vagrant/.ssh/id_ed25519
```

---

## 6) Probar Ansible (ping)

En `control`:
Usa `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg` para evitar que Ansible ignore `ansible.cfg` al ejecutar desde `/vagrant` (directorio compartido world-writable).

```bash
cd /vagrant/ansible
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab
```

✅ Debes ver `SUCCESS` para `web` y `db`.

---

## 7) Variables por familia (Debian vs Rocky)

Crea `group_vars/all.yml`:

```yaml
---
common_packages:
  - vim
  - curl
  - git
```

Crea `group_vars/debian.yml`:

```yaml
---
web_package: nginx
web_service: nginx
```

Crea `group_vars/rocky.yml`:

```yaml
---
db_package: mariadb-server
db_service: mariadb
```

---

## 8) Playbooks por etapas

### 8.1 Playbook 01_bootstrap.yml (mínimo)
Crea `playbooks/01_bootstrap.yml`:

```yaml
---
- name: Pre-bootstrap (asegurar Python en Rocky antes de facts)
  hosts: rocky
  gather_facts: false
  become: true
  tasks:
    - name: Asegurar Python3 en Rocky para habilitar modulos Ansible
      ansible.builtin.raw: |
        test -x /usr/bin/python3 || dnf -y install python3
      changed_when: false

- name: Bootstrap (paquetes básicos y caché de paquetes)
  hosts: lab
  become: true
  tasks:
    - name: Actualizar caché APT (Debian)
      ansible.builtin.apt:
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Actualizar caché DNF (RedHat)
      ansible.builtin.dnf:
        update_cache: yes
      when: ansible_os_family == "RedHat"

    - name: Instalar paquetes comunes
      ansible.builtin.package:
        name: "{{ common_packages }}"
        state: present
```

---

### 8.2 Playbook 02_config.yml (configurar servicios)
Crea `playbooks/02_config.yml`:

```yaml
---
- name: Configurar servidor web (Debian)
  hosts: web
  become: true
  tasks:
    - name: Instalar paquete web
      ansible.builtin.apt:
        name: "{{ web_package }}"
        state: present

    - name: Asegurar servicio web arrancado y habilitado
      ansible.builtin.service:
        name: "{{ web_service }}"
        state: started
        enabled: true

    - name: Publicar página de prueba
      ansible.builtin.copy:
        dest: /var/www/html/index.html
        content: |
          <h1>OK - web configurada por Ansible</h1>
          <p>Host: {{ inventory_hostname }}</p>
          <p>Gestionado por Ansible</p>
        owner: root
        group: root
        mode: "0644"

- name: Configurar base de datos (Rocky)
  hosts: db
  become: true
  tasks:
    - name: Instalar MariaDB Server
      ansible.builtin.dnf:
        name: "{{ db_package }}"
        state: present

    - name: Asegurar servicio DB arrancado y habilitado
      ansible.builtin.service:
        name: "{{ db_service }}"
        state: started
        enabled: true
```

---

### 8.3 Playbook 03_verify.yml (comprobaciones)
Crea `playbooks/03_verify.yml`:

```yaml
---
- name: Verificar web (HTTP)
  hosts: web
  become: false
  tasks:
    - name: Comprobar que el puerto 80 responde
      ansible.builtin.uri:
        url: "http://{{ ansible_host }}/"
        status_code: 200
        return_content: true
      register: web_check

    - name: Fallar si la pagina desplegada no es la esperada
      ansible.builtin.assert:
        that:
          - web_check.status == 200
          - "'OK - web configurada por Ansible' in web_check.content"
        fail_msg: "La verificacion web ha fallado: HTTP o contenido no esperado."
        success_msg: "Verificacion web OK: HTTP 200 y contenido esperado."

- name: Verificar DB (servicio activo)
  hosts: db
  become: true
  tasks:
    - name: Obtener estado del servicio de DB
      ansible.builtin.systemd:
        name: "{{ db_service }}"
      register: db_state
      changed_when: false

    - name: Fallar si MariaDB no esta activa
      ansible.builtin.assert:
        that:
          - db_state.status.ActiveState == "active"
        fail_msg: "MariaDB no esta activa en {{ inventory_hostname }}."
        success_msg: "MariaDB activa en {{ inventory_hostname }}."
```

---

## 9) Orquestar todo con un playbook principal (site.yml)

Crea `site.yml`:

```yaml
---
- import_playbook: playbooks/01_bootstrap.yml
- import_playbook: playbooks/02_config.yml
- import_playbook: playbooks/03_verify.yml
```

---

## 10) Ejecutar el laboratorio con Ansible (y probar idempotencia)

En `control`:

```bash
cd /vagrant/ansible
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml
```

✅ Debe terminar sin fallos.

### 10.1 Idempotencia (ejecútalo otra vez)
```bash
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml
```

La segunda vez, la mayoría de tareas deberían mostrar `ok` en lugar de `changed`.

---

## 11) Validación manual rápida (desde `control`)

Ver web:

```bash
curl -s http://192.168.56.11 | head
```

Ver DB:

```bash
ssh -i /home/vagrant/.ssh/id_ed25519 vagrant@192.168.56.12 "systemctl is-active mariadb"
```

---

## 12) Evidencias a guardar (para el informe final)

Guarda salida/capturas de:

- `ansible --version`
- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab`
- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` (primera y segunda ejecución)
- `curl http://192.168.56.11`
- `systemctl is-active mariadb` en `db`

---

## Checkpoint de la Fase 3

- [ ] `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` → SUCCESS en web y db
- [ ] `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` → termina OK
- [ ] `curl http://192.168.56.11` muestra la página de Ansible
- [ ] `systemctl is-active mariadb` en db devuelve `active`
- [ ] Segunda ejecución del playbook muestra pocos `changed` (idempotente)
