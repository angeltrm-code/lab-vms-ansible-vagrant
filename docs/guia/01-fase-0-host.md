# Fase 0 — Preparación del “terreno” (HOST)

> Proyecto: **Lab VMs + VirtualBox + Vagrant + Ansible**  
> Público objetivo: **personas sin experiencia**  
> Objetivo de esta fase: dejar tu PC (HOST) listo para ejecutar máquinas virtuales sin problemas y con una carpeta de proyecto ordenada y reproducible.

---

## 0) Qué vas a conseguir al terminar esta fase

✅ Tendrás:

- VirtualBox instalado y funcionando.
- Vagrant instalado y funcionando.
- (Opcional) Git y VS Code preparados.
- Virtualización activada (VT-x/AMD-V).
- Una carpeta de proyecto lista para empezar (donde luego pondrás `Vagrantfile` y `ansible/`).
- Checkpoints claros para saber si está todo OK.

---

## 1) Conceptos rápidos (para no perderse)

- **HOST**: tu PC real (Windows/Linux).
- **VM**: máquina virtual (un “PC” dentro de tu PC).
- **VirtualBox**: crea y ejecuta VMs.
- **Vagrant**: automatiza la creación de VMs (lee `Vagrantfile`).
- **Ansible**: automatiza la configuración dentro de las VMs (desde `control`).

---

## 2) Requisitos mínimos recomendados

### Hardware (orientativo)
Para 3 VMs (control/web/db), recomendado:
- CPU: 4 núcleos o más
- RAM: 16 GB ideal (8 GB puede ir justo)
- Disco libre: 30–60 GB

### Sistema operativo del host
- Windows 10/11 o Linux (cualquier distro moderna)

---

## 3) Activar virtualización en BIOS/UEFI (muy importante)

Si la virtualización no está activada, VirtualBox puede fallar o ir lento.

### 3.1 Cómo se llama la opción
- Intel: **Intel VT-x**
- AMD: **SVM / AMD-V**

### 3.2 Cómo comprobar síntomas típicos
- VirtualBox no arranca VMs
- Error de “VT-x is not available”
- Rendimiento muy malo

✅ Solución: entrar en BIOS/UEFI y activar VT-x/AMD-V.

> Nota: cada placa es distinta, pero suele estar en:
> - Advanced / CPU Configuration / Virtualization

---

## 4) Instalar VirtualBox

### 4.1 Qué instalar
- **VirtualBox** (recomendado: versión estable más reciente)
- (Opcional) **Extension Pack** (USB 2/3, RDP, etc.)

### 4.2 Comprobación básica (en el host)
Una vez instalado, abre VirtualBox y asegúrate de que:
- abre sin errores
- puedes ver el panel principal

**Checkpoint CLI (si está disponible):**
```bash
VBoxManage --version
```

✅ Debe devolver una versión (por ejemplo `7.x.x`).

---

## 5) Instalar Vagrant

### 5.1 Comprobación (en el host)
```bash
vagrant --version
```

✅ Debe devolver una versión (por ejemplo `Vagrant 2.x.x`).

### 5.2 Comprobación del provider VirtualBox
Vagrant usará VirtualBox como provider por defecto si está instalado.

Opcional:
```bash
vagrant plugin list
```

---

## 6) (Opcional) Instalar Git y VS Code

### 6.1 Git
Te ayuda a versionar el laboratorio:

```bash
git --version
```

### 6.2 VS Code
Recomendado para editar `Vagrantfile`, playbooks y documentos:
- Instala extensión YAML (opcional)
- Instala extensión Ansible (opcional)

---

## 7) Crear la carpeta del laboratorio (estructura inicial)

El objetivo es que TODO quede dentro de una carpeta para que sea reproducible.

### 7.1 Crear carpeta (Windows)
En PowerShell:

```powershell
mkdir lab-vagrant-ansible
cd lab-vagrant-ansible
```

### 7.2 Crear carpeta (Linux)
```bash
mkdir -p lab-vagrant-ansible
cd lab-vagrant-ansible
```

### 7.3 Estructura recomendada desde el inicio
Crea estas carpetas (mínimas):

```bash
mkdir -p ansible docs/evidencias
```

Tu estructura quedará así:

```
lab-vagrant-ansible/
├─ Vagrantfile          (se creará en Fase 2)
├─ ansible/             (inventario, playbooks)
└─ docs/
   └─ evidencias/       (capturas y logs)
```

---

## 8) (Opcional) Inicializar Git en el proyecto

Dentro de la carpeta:

```bash
git init
```

Crea un `.gitignore` básico (opcional pero útil):

```bash
cat > .gitignore <<'EOF'
.vagrant/
*.log
docs/evidencias/*
EOF
```

> Nota: si quieres subir evidencias al repo, quita la línea `docs/evidencias/*`.

---

## 9) Prueba rápida: “¿todo está listo?”

### 9.1 Checkpoints obligatorios (mínimo)
Ejecuta en el host:

```bash
vagrant --version
VBoxManage --version
```

✅ Ambos deben funcionar.

### 9.2 Checkpoint opcional (muy recomendado)
Si VirtualBox está bien, al final del proyecto podrás crear VMs sin errores.
De momento, lo más importante es que:
- Vagrant y VirtualBox están instalados
- virtualización activada

---

## 10) Problemas típicos en HOST (y cómo identificarlos)

### 10.1 Windows: VirtualBox lento o errores por Hyper-V
**Síntomas:**
- VMs lentas
- errores al arrancar
- VirtualBox no puede acceder a VT-x

**Causa típica:**
- Hyper-V / Virtual Machine Platform / WSL2 interfieren (depende de la versión y configuración)

**Acción recomendada:**
- No “toques todo” a ciegas.
- Primero confirma el error exacto (mensaje de VirtualBox).
- Luego aplicamos la corrección mínima necesaria.

---

### 10.2 Falta de espacio en disco
**Síntomas:**
- `vagrant up` falla al crear discos

**Solución:**
- liberar espacio
- evitar boxes pesadas
- limitar número de VMs al principio

---

## 11) Evidencias de la Fase 0 (para el informe final)
Guarda la salida (o captura) de:

- `vagrant --version`
- `VBoxManage --version`
- (opcional) `git --version`

Ubicación recomendada:
- `docs/evidencias/fase0_host/01_versions.txt`

---

## Checkpoint final de Fase 0 (lo que debe estar OK)

- [ ] Virtualización activada (VT-x / AMD-V)
- [ ] VirtualBox instalado y abre
- [ ] `VBoxManage --version` funciona
- [ ] Vagrant instalado
- [ ] `vagrant --version` funciona
- [ ] Carpeta `lab-vagrant-ansible/` creada con `ansible/` y `docs/evidencias/`

---

## Próximo paso (Fase 1)
Ahora sí: diseñaremos el laboratorio (VMs, roles, redes, IPs y recursos) y dejaremos todo listo para escribir el `Vagrantfile`.
