# Vagrant y Ansible (explicación para principiantes)

> Objetivo: entender qué es cada herramienta, qué problema resuelve y cómo encajan en un laboratorio con VirtualBox.

---

## 1) Vagrant

### ¿Qué es Vagrant?
**Vagrant** es una herramienta para **crear y gestionar máquinas virtuales de forma automática y repetible**.  
En vez de configurar cada VM “a mano” en VirtualBox, describes cómo debe ser tu laboratorio en un archivo llamado **`Vagrantfile`** y Vagrant lo construye igual siempre.

### ¿Para qué sirve?
- **Repetibilidad**: todos obtienen las mismas VMs con la misma configuración.
- **Rapidez**: levantar 3–5 VMs pasa de “mucho rato” a “minutos”.
- **Orden**: tu lab queda definido como código (Infrastructure as Code, en pequeño).
- **Facilidad para practicar**: puedes destruir y recrear el lab sin miedo.

### ¿Cómo funciona? (idea mental)
Piensa en Vagrant como una “receta”:

- **Receta** → `Vagrantfile` (CPU, RAM, redes, nombre de VMs…)
- **Plantilla base** → **Box** (imagen base de Debian/Rocky/Ubuntu…)
- **Cocina** → **Provider** (VirtualBox, normalmente)
- **Resultado** → máquinas listas con **SSH** funcionando

**Flujo típico:**
1. Tú escribes o modificas el `Vagrantfile`.
2. Ejecutas `vagrant up`.
3. Vagrant descarga la **box** si no existe.
4. VirtualBox crea las VMs con esa configuración.
5. Vagrant deja las VMs accesibles (SSH, red, etc.).

### Conceptos clave
- **Box**: imagen base (como “Debian minimal”) usada para crear VMs.
- **Provider**: quién ejecuta la VM (por ejemplo, **VirtualBox**).
- **Vagrantfile**: el archivo que define tu lab.
- **Provisioning**: configuración extra al crear la VM (por ejemplo, instalar paquetes con shell o usar Ansible).

### Comandos de Vagrant que usarás siempre
- `vagrant init` → crea un `Vagrantfile` inicial
- `vagrant up` → crea/arranca las VMs
- `vagrant status` → muestra el estado de las VMs
- `vagrant ssh <vm>` → entra a una VM por SSH
- `vagrant halt` → apaga las VMs
- `vagrant reload` → reinicia aplicando cambios
- `vagrant destroy` → borra las VMs (se pueden recrear después)

---

## 2) Ansible

### ¿Qué es Ansible?
**Ansible** es una herramienta de **automatización y configuración**.  
Sirve para instalar paquetes, crear usuarios, editar configuraciones, habilitar servicios, etc. en una o muchas máquinas **de forma consistente**.

### ¿Para qué sirve?
- Evita configurar servidores “a mano”.
- Puedes aplicar la misma configuración en 1, 3 o 50 máquinas.
- Es **idempotente**: si algo ya está bien, no lo repite ni lo rompe.
- Deja “documentado” el estado deseado: el playbook es la receta.

### ¿Cómo funciona? (idea mental)
Piensa en Ansible como un “robot” que entra por SSH y aplica instrucciones:

- **Inventario**: lista de máquinas (control, web-nginx, db-mariadb…)
- **Playbooks**: recetas en YAML (qué tareas ejecutar)
- **Conexión**: normalmente **SSH**
- **Resultado**: máquinas en el estado deseado (“así debe estar”)

**Flujo típico:**
1. Tienes un nodo **control** (controller) desde donde ejecutas Ansible.
2. Defines un **inventario** (hosts y grupos).
3. Ejecutas un **playbook**.
4. Ansible conecta por SSH a cada máquina y aplica tareas.
5. Repetible: lo vuelves a ejecutar cuando quieras.

### Piezas básicas de Ansible
- **Inventario**: define hosts y grupos (por ejemplo `web-nginx`, `db-mariadb`, `webservers`, `dbservers`).
- **Playbook**: archivo YAML que contiene tareas.
- **Módulos**: acciones ya preparadas (instalar paquetes, gestionar servicios, copiar archivos…).
- **Roles**: estructura organizada de playbooks (útil cuando el proyecto crece).

### Comandos de Ansible esenciales
- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` → prueba conectividad con todas las máquinas del inventario
- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` → ejecuta el playbook principal
- `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-inventory --list` → muestra el inventario resuelto

---

## 3) Cómo encajan VirtualBox + Vagrant + Ansible

- **VirtualBox**: ejecuta las VMs (el “hardware virtual”).
- **Vagrant**: crea y administra las VMs rápidamente (infra del laboratorio).
- **Ansible**: configura lo que hay dentro de las VMs (software y servicios).

**Foto completa del laboratorio:**
1. **Vagrant** levanta las máquinas (control/web-nginx/db-mariadb) con IPs estables y SSH.
2. **Ansible** instala y configura servicios (nginx, mariadb, usuarios, firewall…).
3. Validamos que el lab funciona y queda reproducible.

---

## 4) Ejemplo mental (sin código)
Queremos 3 VMs: `control`, `web-nginx`, `db-mariadb`  
- Vagrant: crea las 3 VMs, les asigna IP privada fija y habilita acceso por SSH.
- Ansible: instala **nginx** en `web-nginx`, **mariadb** en `db-mariadb`, y deja todo configurado.

---

## 5) Checklist antes de empezar el lab
- [ ] VirtualBox instalado
- [ ] Vagrant instalado (`vagrant --version`)
- [ ] Virtualización activada en BIOS/UEFI (VT-x/AMD-V)
- [ ] Carpeta del proyecto creada (todo dentro)
- [ ] Objetivo claro: empezar por 1 VM → luego 3 VMs → luego Ansible

---

**Siguiente paso del proyecto:** crear la estructura del laboratorio y el primer `Vagrantfile` comentado, y después el inventario/playbook mínimo de Ansible.
