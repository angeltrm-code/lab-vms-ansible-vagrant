# Guia rapida de uso (inicio en 10-15 minutos)

Esta guia es para levantar y validar el laboratorio con friccion minima.
Usa el estado tecnico actual del proyecto:

- VMs: `control`, `web-nginx`, `db-mariadb`
- Ansible: ejecutar siempre con `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg`

---

## 1) Requisitos previos (host)

En la raiz del proyecto (`C:\Users\usuario\lab-vms-ansible-vagrant`), valida herramientas:

```bash
vagrant --version
VBoxManage --version
```

Si estos dos comandos fallan, instala/corrige Vagrant y VirtualBox antes de seguir.

---

## 2) Levantar laboratorio (host)

```bash
vagrant up
vagrant status
```

Debes ver `control`, `web-nginx` y `db-mariadb` en estado `running`.

---

## 3) Entrar a control e instalar Ansible

```bash
vagrant ssh control
sudo apt update
sudo apt install -y ansible openssh-client
```

---

## 4) Preparar SSH de control hacia nodos

```bash
ssh-keygen -t ed25519 -N "" -f /home/vagrant/.ssh/id_ed25519
ssh-copy-id -i /home/vagrant/.ssh/id_ed25519.pub vagrant@192.168.56.11
ssh-copy-id -i /home/vagrant/.ssh/id_ed25519.pub vagrant@192.168.56.12
```

Si ya existia la clave, puedes aceptar sobreescritura o reutilizarla.

---

## 5) Ejecutar Ansible correctamente

```bash
cd /vagrant/ansible
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml
```

Esperado:

- `ansible -m ping lab`: `SUCCESS` en `web-nginx` y `db-mariadb`
- segunda ejecucion del playbook: `changed` bajo (idealmente `0`)

---

## 6) Validar servicios finales

```bash
curl -s http://192.168.56.11 | head
ssh -i /home/vagrant/.ssh/id_ed25519 vagrant@192.168.56.12 "systemctl is-active mariadb"
```

Esperado:

- web responde con contenido HTML de prueba
- MariaDB responde `active`

---

## 7) Errores tipicos (solucion rapida)

1. Error: Ansible ignora `ansible.cfg`.
   Solucion: usa siempre el prefijo `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg`.

2. Error: `UNREACHABLE` por SSH en `ansible -m ping lab`.
   Solucion: repite `ssh-copy-id` a `192.168.56.11` y `192.168.56.12`.

3. Error: VM bloqueada (`Vagrant machine is locked`).
   Solucion: espera unos segundos y repite el comando (no lances varios `vagrant` en paralelo).

4. Error: host key cambio despues de recrear VM.
   Solucion: desde `control`, limpia `known_hosts` para esa IP y repite `ssh-copy-id`.

---

## 8) Checklist breve final

- [ ] `vagrant status` muestra `control`, `web-nginx`, `db-mariadb` en `running`
- [ ] `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` da `SUCCESS`
- [ ] `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` funciona 2 veces
- [ ] `curl http://192.168.56.11` responde
- [ ] `systemctl is-active mariadb` en `db-mariadb` devuelve `active`

