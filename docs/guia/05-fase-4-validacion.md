# Fase 4 — Validación (evidencias)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Público objetivo: **personas sin experiencia**  
> Objetivo de esta fase: **demostrar con pruebas claras (y evidencias)** que el laboratorio funciona: red, SSH, servicios y automatización.  
> Resultado: una carpeta de evidencias ordenada y un checklist final “apto para entregar”.

---

## 0) Qué vas a conseguir al terminar esta fase

✅ Tendrás:

- Un conjunto de **comandos de verificación** (host + VMs).
- Un sistema de **evidencias** ordenado en carpetas.
- Un checklist final donde puedas marcar: “todo OK”.

> Importante: la validación no es “porque yo lo digo”.  
> Es “mira: este comando prueba X y su salida lo demuestra”.

---

## 1) Cómo guardar evidencias (recomendación práctica)

Para que sea fácil de revisar, guardaremos:
- capturas (screenshots)
- salidas de comandos en archivos `.txt` o `.log`

### 1.1 Estructura de evidencias (en el host)
Dentro del proyecto, crea:

```
docs/
└─ evidencias/
   ├─ fase2_vagrant/
   ├─ fase3_ansible/
   ├─ fase4_validacion/
   └─ resumen/
```

> Si prefieres, puedes numerar: `01_`, `02_`, etc. Lo importante es que sea consistente.

### 1.2 Nombres recomendados de evidencias
Usa nombres con orden + descripción:

- `01_vagrant_status.txt`
- `02_control_ip-a.txt`
- `03_ping_control_to_web_nginx.txt`
- `10_ansible_ping_lab.txt`
- `20_curl_web_nginx_index.txt`
- `30_db_mariadb_active.txt`

---

## 2) Validación en el HOST (tu PC)

> Estos comandos se ejecutan **en tu host**, en la carpeta del proyecto.

### 2.1 Versiones (prueba de herramientas instaladas)
Guarda salida en:
- `docs/evidencias/fase4_validacion/01_versions.txt`

Comandos:

```bash
vagrant --version
VBoxManage --version
```

---

### 2.2 Estado de Vagrant (prueba de que las VMs están arriba)
Guarda en:
- `docs/evidencias/fase4_validacion/02_vagrant_status.txt`

Comando:

```bash
vagrant status
```

---

### 2.3 Acceso SSH desde el host (prueba básica)
Guarda en:
- `docs/evidencias/fase4_validacion/03_vagrant_ssh_web_nginx_hostname.txt`

Comando (ejemplo con web-nginx):

```bash
vagrant ssh web-nginx -c "hostname && ip a | head -n 25"
```

---

## 3) Validación de red dentro de las VMs

> Esta validación se hace entrando a cada VM con `vagrant ssh`.

---

### 3.1 En `control`: IPs y rutas
Guarda en:
- `docs/evidencias/fase4_validacion/10_control_network.txt`

En el host:

```bash
vagrant ssh control
```

Dentro de `control`:

```bash
ip a
ip r
```

✅ Debes ver:
- `192.168.56.10` (Host-Only)
- otra IP NAT (`10.0.2.x` o similar)
- una ruta por defecto vía NAT (para Internet)

---

### 3.2 Pings internos (Host-Only)
Guarda en:
- `docs/evidencias/fase4_validacion/11_ping_control_to_web_nginx_db_mariadb.txt`

En `control`:

```bash
ping -c 2 192.168.56.11
ping -c 2 192.168.56.12
```

✅ Prueba:
- conectividad entre máquinas en red privada

---

### 3.3 Ping a Internet (NAT)
Guarda en:
- `docs/evidencias/fase4_validacion/12_ping_control_internet.txt`

En `control`:

```bash
ping -c 2 1.1.1.1
```

Opcional (DNS):

```bash
ping -c 2 deb.debian.org
```

✅ Prueba:
- salida a Internet y resolución DNS (si haces el segundo)

---

## 4) Validación de Ansible (desde `control`)

### 4.1 Ping Ansible al inventario
Guarda en:
- `docs/evidencias/fase4_validacion/20_ansible_ping_lab.txt`

En `control`:

```bash
cd /vagrant/ansible
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab
```

✅ Prueba:
- conexión SSH automatizada y control remoto

---

### 4.2 Ejecución idempotente del playbook
Guarda en:
- `docs/evidencias/fase4_validacion/21_ansible_playbook_first_run.txt`
- `docs/evidencias/fase4_validacion/22_ansible_playbook_second_run.txt`

Comandos:

```bash
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml
ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml
```

✅ Prueba:
- primera ejecución configura
- segunda ejecución “casi todo ok” (idempotencia)

> Consejo: en la segunda ejecución, busca que haya pocos `changed`.

---

## 5) Validación del servicio WEB (HTTP)

### 5.1 Comprobar desde `control` con curl
Guarda en:
- `docs/evidencias/fase4_validacion/30_curl_web_nginx_from_control.txt`

En `control`:

```bash
curl -i http://192.168.56.11 | head -n 20
```

✅ Prueba:
- el servicio web responde en puerto 80

---

### 5.2 Comprobar contenido “OK - web configurada por Ansible”
Guarda en:
- `docs/evidencias/fase4_validacion/31_web_nginx_content_check.txt`

En `control`:

```bash
curl -s http://192.168.56.11 | grep -i "OK - web configurada por Ansible"
```

✅ Prueba:
- no solo responde, sino que es la página “nuestra” (deploy correcto)

---

### 5.3 (Opcional) Acceso desde el HOST
Si tu host puede acceder a la red Host-Only, prueba desde el host:

```bash
curl -i http://192.168.56.11 | head -n 10
```

Si usas Windows sin `curl` o con alias raro, abre en navegador:
- `http://192.168.56.11`

✅ Prueba:
- el host ve el servicio en la red privada del lab.

---

## 6) Validación del servicio DB (MariaDB)

### 6.1 Comprobar estado del servicio en `db-mariadb`
Guarda en:
- `docs/evidencias/fase4_validacion/40_db_mariadb_status.txt`

En el host:

```bash
vagrant ssh db-mariadb
```

Dentro de `db-mariadb`:

```bash
systemctl is-active mariadb
systemctl status mariadb --no-pager | head -n 30
```

✅ Prueba:
- servicio activo y gestionado por systemd

---

### 6.2 (Opcional) Comprobar puerto 3306 escuchando
Guarda en:
- `docs/evidencias/fase4_validacion/41_db_port_3306.txt`

En `db-mariadb`:

```bash
sudo ss -lntp | grep 3306 || sudo ss -lnt | grep 3306
```

✅ Prueba:
- MariaDB escucha (local o en red según configuración)

---

## 7) Validación cruzada (web-nginx ↔ db-mariadb) — opcional avanzado
> Solo si en fases futuras configuramos que web-nginx use db-mariadb.  
> Por ahora, con “db-mariadb activo” vale, pero dejamos la guía preparada.

Ejemplo de prueba desde `web-nginx` hacia `db-mariadb`:

```bash
vagrant ssh web-nginx -c "nc -vz 192.168.56.12 3306 || true"
```

✅ Prueba:
- conectividad al puerto DB (si `nc` está instalado)

---

## 8) Checkpoint final de Fase 4 (tabla “OK/NO”)

Marca cada punto:

| Área | Prueba | Resultado esperado | OK |
|---|---|---|---|
| Herramientas host | `vagrant --version`, `VBoxManage --version` | muestran versión | ⬜ |
| Vagrant | `vagrant status` | 3 VMs en `running` | ⬜ |
| Red VM | `ip a` en cada VM | IP Host-Only correcta | ⬜ |
| Ping interno | `ping control → web-nginx/db-mariadb` | responde | ⬜ |
| Internet NAT | `ping 1.1.1.1` | responde | ⬜ |
| Ansible conectividad | `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` | SUCCESS | ⬜ |
| Playbook | `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` | sin fallos | ⬜ |
| Idempotencia | segunda ejecución | pocos `changed` | ⬜ |
| Web HTTP | `curl http://192.168.56.11` | 200 OK / HTML | ⬜ |
| DB service | `systemctl is-active mariadb` | `active` | ⬜ |

---

## 9) Entregable final de evidencias (qué debe existir)

En tu proyecto deben quedar:

- `docs/evidencias/fase4_validacion/` con:
  - `01_versions.txt`
  - `02_vagrant_status.txt`
  - `10_control_network.txt`
  - `11_ping_control_to_web_nginx_db_mariadb.txt`
  - `20_ansible_ping_lab.txt`
  - `21_ansible_playbook_first_run.txt`
  - `22_ansible_playbook_second_run.txt`
  - `30_curl_web_nginx_from_control.txt`
  - `40_db_mariadb_status.txt`
  - (opcionales) `31_web_nginx_content_check.txt`, `41_db_port_3306.txt`

> Si el profesor/cliente te pide “pruebas”, aquí están, limpias y ordenadas.

---

## Próxima fase (Fase 5)
En la Fase 5 añadiremos:
- tabla de troubleshooting (error → causa → solución)
- hardening ligero: actualizaciones, SSH por clave, firewall básico
- checklist de seguridad mínima viable
