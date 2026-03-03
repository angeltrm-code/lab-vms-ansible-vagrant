# Fase 2 — Provisionado con Vagrant + VirtualBox (crear el “esqueleto” del laboratorio)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Público objetivo: **personas sin experiencia**  
> Objetivo de esta fase: **crear y levantar las VMs automáticamente** con Vagrant en VirtualBox, con **NAT + Host-Only**, IPs fijas y acceso SSH funcionando.

---

## 0) Qué vas a conseguir al terminar esta fase

✅ Tendrás 3 VMs creadas y arrancadas:

- `control` (Debian) → 192.168.56.10  
- `web` (Debian) → 192.168.56.11  
- `db` (Rocky Linux) → 192.168.56.12  

✅ Podrás entrar por SSH con:

- `vagrant ssh control`
- `vagrant ssh web`
- `vagrant ssh db`

✅ Podrás comprobar IPs dentro de cada VM y hacer ping entre ellas.

---

## 1) Preparar la carpeta del laboratorio

### 1.1 Crear la carpeta (en el host)
Crea una carpeta de proyecto. Ejemplo (Windows):

```powershell
mkdir lab-vagrant-ansible
cd lab-vagrant-ansible
```

Ejemplo (Linux):

```bash
mkdir -p lab-vagrant-ansible
cd lab-vagrant-ansible
```

### 1.2 (Opcional) Iniciar Git
Si quieres control de versiones:

```bash
git init
```

---

## 2) Inicializar Vagrant

Dentro de la carpeta del proyecto:

```bash
vagrant init
```

Esto crea un archivo llamado **`Vagrantfile`**.

---

## 3) Configurar VirtualBox: red Host-Only (si no existe)

> Importante: muchas veces VirtualBox ya trae una Host-Only creada (por ejemplo `vboxnet0`).  
> Si ya existe, puedes usarla tal cual.

### 3.1 Ver si existe una Host-Only
En el host:

```bash
VBoxManage list hostonlyifs
```

Si ves una interfaz tipo `vboxnet0`, perfecto.

### 3.2 Crear una Host-Only (si no hay)
En algunos sistemas puedes crearla así (si te da errores, usa la GUI de VirtualBox):

```bash
VBoxManage hostonlyif create
```

> Alternativa GUI: VirtualBox → **Herramientas** → **Red** (o “Network”) → **Host-only Networks** → crear.

### 3.3 Comprobar la IP de la Host-Only (recomendado)
Lo normal es que sea `192.168.56.1/24` en el host.  
Si no lo es, lo ajustaremos más adelante (en Vagrant puedes fijar IPs igualmente).

---

## 4) Elegir “boxes” (imágenes base)

Vagrant usa “boxes” como plantilla.

### 4.1 Boxes recomendadas (muy usadas)
- Debian: `debian/bookworm64` (válida y común)
- Rocky: `rockylinux/9`

> Nota: en el futuro puedes cambiar a Debian 13 cuando la box esté disponible/estable, pero para un lab didáctico estas funcionan muy bien.

---

## 5) Crear el Vagrantfile del laboratorio (copiar/pegar)

Abre el archivo `Vagrantfile` y reemplázalo por esto:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Recomendación: evita actualizaciones automáticas del guest para que el lab sea más predecible.
  # config.vm.box_check_update = false

  # Red Host-Only (privada) para IPs fijas del laboratorio
  # NAT se crea automáticamente como primera interfaz en VirtualBox.
  LAB_NET = "192.168.56."

  # =========================
  # VM: control (Debian)
  # =========================
  config.vm.define "control" do |control|
    control.vm.box = "debian/bookworm64"
    control.vm.hostname = "control"
    control.vm.network "private_network", ip: "#{LAB_NET}10"

    control.vm.provider "virtualbox" do |vb|
      vb.name = "lab-control"
      vb.cpus = 1
      vb.memory = 1536
    end
  end

  # =========================
  # VM: web (Debian)
  # =========================
  config.vm.define "web" do |web|
    web.vm.box = "debian/bookworm64"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "#{LAB_NET}11"

    web.vm.provider "virtualbox" do |vb|
      vb.name = "lab-web"
      vb.cpus = 1
      vb.memory = 1024
    end
  end

  # =========================
  # VM: db (Rocky)
  # =========================
  config.vm.define "db" do |db|
    db.vm.box = "rockylinux/9"
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "#{LAB_NET}12"

    db.vm.provider "virtualbox" do |vb|
      vb.name = "lab-db"
      vb.cpus = 2
      vb.memory = 2048
    end
  end
end
```

### 5.1 Qué hace este Vagrantfile (explicado fácil)
- Crea 3 VMs en VirtualBox.
- A cada VM le pone:
  - **NAT** (automático) → salida a Internet
  - **Host-Only** (`private_network`) → IP fija 192.168.56.X
- Ajusta CPU/RAM para que funcione fluido.
- Pone hostnames coherentes: `control`, `web`, `db`.

---

## 6) Levantar las máquinas

En la carpeta del proyecto:

```bash
vagrant up
```

La primera vez:
- tarda más, porque descarga las boxes.

---

## 7) Comprobar estado y entrar por SSH

### 7.1 Estado
```bash
vagrant status
```

Deberías ver las 3 en `running`.

### 7.2 Entrar por SSH
```bash
vagrant ssh control
```

En otra terminal:
```bash
vagrant ssh web
```

Y:
```bash
vagrant ssh db
```

---

## 8) Verificar IPs dentro de cada VM (Checkpoint real)

Dentro de **control**:

```bash
ip a
```

Busca algo como:
- `192.168.56.10` (Host-Only)
- otra IP (NAT), normalmente `10.0.2.15` o similar

Haz lo mismo en **web** y **db**.

---

## 9) Verificar conectividad entre VMs (muy importante)

En `control`:

```bash
ping -c 2 192.168.56.11
ping -c 2 192.168.56.12
```

En `web`:

```bash
ping -c 2 192.168.56.10
ping -c 2 192.168.56.12
```

En `db`:

```bash
ping -c 2 192.168.56.10
ping -c 2 192.168.56.11
```

✅ Si todo responde, la red Host-Only está perfecta.

---

## 10) Verificar salida a Internet (NAT)

En Debian (control/web):

```bash
ping -c 2 1.1.1.1
```

Y también:

```bash
ping -c 2 deb.debian.org
```

En Rocky (db):

```bash
ping -c 2 1.1.1.1
ping -c 2 rockylinux.org
```

> Si `1.1.1.1` funciona pero los dominios no, es problema de DNS dentro de la VM.

---

## 11) Problemas típicos y soluciones rápidas

### 11.1 “No se puede asignar IP privada” o conflicto 192.168.56.0/24
Causa: ya usas esa red en otro sitio.

Solución: cambia el prefijo, por ejemplo:

- `192.168.57.10/11/12`

En el Vagrantfile cambia:

```ruby
LAB_NET = "192.168.57."
```

Y luego:

```bash
vagrant reload
```

---

### 11.2 DNS no resuelve (ping IP sí, ping dominio no)
Solución rápida (Debian/Ubuntu):

```bash
sudo resolvectl status
cat /etc/resolv.conf
```

A veces reiniciar la red ayuda:

```bash
sudo systemctl restart systemd-resolved
```

En Rocky:

```bash
nmcli dev status
cat /etc/resolv.conf
```

> Si el problema viene de VirtualBox/NAT network, se resuelve en la configuración del host, pero primero confirmamos síntomas.

---

### 11.3 Las VMs no se pueden crear (VirtualBox en conflicto con Hyper-V)
En Windows, si VirtualBox falla o va muy lento, revisar:
- Hyper-V / WSL2 / Virtual Machine Platform

No lo cambiamos aún: primero confirmamos el error exacto.

---

## 12) Checkpoint de la Fase 2 (lo mínimo para seguir)

✅ Debes poder demostrar:
- [ ] `vagrant status` muestra `control`, `web`, `db` en `running`
- [ ] `vagrant ssh control` funciona
- [ ] `ip a` muestra `192.168.56.10/11/12` en cada VM
- [ ] `ping` entre VMs por Host-Only funciona
- [ ] `ping 1.1.1.1` funciona (salida NAT)

---

## 13) Qué guardamos como evidencias (para el informe final)
Guarda capturas o copia/pega de estos comandos:

1) En host:
- `vagrant --version`
- `VBoxManage --version`
- `vagrant status`

2) En cada VM:
- `ip a`
- `ping -c 2 192.168.56.XX` (entre VMs)
- `ping -c 2 1.1.1.1` (Internet)

---

## Próximo paso (Fase 3)
Con las VMs arriba, en la Fase 3 haremos:
- instalar Ansible en `control`
- inventario con `web` y `db`
- `ansible -m ping all`
- playbooks por etapas: bootstrap → config → verify
