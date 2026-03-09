# Empaquetado final de la guía (cómo dejarlo “bonito” y entregable)

> Objetivo: pasar de “varios .md sueltos” a una guía organizada, fácil de navegar y lista para entregar (clase, portfolio o documentación interna).

---

## 1) Qué vamos a empaquetar

Vamos a unificar estos documentos en una estructura clara:

- `vagrant-y-ansible-explicacion.md`
- `hoja-de-ruta-lab-vms-ansible-vagrant.md`
- `fase-0-preparacion-host.md`
- `fase-1-diseno-del-laboratorio.md`
- `fase-2-provisionado-vagrant-virtualbox.md`
- `fase-3-ansible-automatizacion.md`
- `fase-4-validacion-evidencias.md`
- `fase-5-troubleshooting-hardening.md`

Y añadiremos:
- un `README.md` con índice y estructura
- una convención para evidencias (`docs/evidencias/`)

---

## 2) Estructura final recomendada (carpetas)

Dentro de tu proyecto `lab-vms-ansible-vagrant/`:

```
lab-vms-ansible-vagrant/
├─ Vagrantfile
├─ ansible/
├─ docs/
│  ├─ guia/
│  │  ├─ 00-indice-guia.md
│  │  ├─ 00-conceptos-vagrant-ansible.md
│  │  ├─ 00-hoja-de-ruta.md
│  │  ├─ 01-fase-0-host.md
│  │  ├─ 02-fase-1-diseno.md
│  │  ├─ 03-fase-2-vagrant.md
│  │  ├─ 04-fase-3-ansible.md
│  │  ├─ 05-fase-4-validacion.md
│  │  └─ 06-fase-5-hardening.md
│  └─ evidencias/
│     ├─ fase0_host/
│     ├─ fase2_vagrant/
│     ├─ fase3_ansible/
│     ├─ fase4_validacion/
│     └─ fase5_hardening/
└─ README.md
```

### 2.1 ¿Por qué renombrar?
- Los nombres con número mantienen el orden.
- Es más fácil de seguir y revisar.

> Si prefieres mantener los nombres originales, también vale.  
> Esta estructura es para dejarlo “entregable y profesional”.

---

## 3) Instrucciones para empaquetar (pasos en el host)

### 3.1 Crear carpetas de documentación
Desde la carpeta del proyecto:

```bash
mkdir -p docs/guia docs/evidencias
```

### 3.2 Mover/copiar los .md a `docs/guia/`
Copia los archivos de la guía a `docs/guia/` y renómbralos siguiendo el esquema.

Ejemplo de correspondencias:

| Archivo actual | Archivo final (recomendado) |
|---|---|
| `vagrant-y-ansible-explicacion.md` | `docs/guia/00-conceptos-vagrant-ansible.md` |
| `hoja-de-ruta-lab-vms-ansible-vagrant.md` | `docs/guia/00-hoja-de-ruta.md` |
| `fase-0-preparacion-host.md` | `docs/guia/01-fase-0-host.md` |
| `fase-1-diseno-del-laboratorio.md` | `docs/guia/02-fase-1-diseno.md` |
| `fase-2-provisionado-vagrant-virtualbox.md` | `docs/guia/03-fase-2-vagrant.md` |
| `fase-3-ansible-automatizacion.md` | `docs/guia/04-fase-3-ansible.md` |
| `fase-4-validacion-evidencias.md` | `docs/guia/05-fase-4-validacion.md` |
| `fase-5-troubleshooting-hardening.md` | `docs/guia/06-fase-5-hardening.md` |

> En Windows, si no quieres usar comandos, puedes hacerlo arrastrando con el explorador.

---

## 4) Crear un índice dentro de `docs/guia/` (00-indice-guia.md)

Este archivo sirve como “menú” dentro de `docs/guia/`.

Contenido recomendado:

- Enlaces a cada fase
- Qué se consigue en cada una
- Checkpoints rápidos

> Si quieres, lo generamos automáticamente en el siguiente paso.

---

## 5) Evidencias (carpetas estándar)

Crea estas carpetas:

```
docs/evidencias/fase0_host/
docs/evidencias/fase2_vagrant/
docs/evidencias/fase3_ansible/
docs/evidencias/fase4_validacion/
docs/evidencias/fase5_hardening/
```

Y guarda dentro:
- `*.txt` (salidas de comandos)
- `*.png` (capturas)

---

## 6) README final del proyecto (en raíz)

El `README.md` debe:
- explicar objetivo del laboratorio
- explicar estructura
- enlazar a la guía en `docs/guia/`
- incluir checklist final

> Ya tienes un README listo en este paquete.

---

## 7) Checkpoint del empaquetado

- [ ] Existe `README.md` en la raíz
- [ ] Existe `docs/guia/` con todos los .md en orden
- [ ] Existe `docs/evidencias/` con subcarpetas por fase
- [ ] Puedes seguir la guía desde el índice sin perderte

---

## 8) Entregable final (qué entregar)

Para entregar el proyecto como documentación:
- carpeta `docs/guia/`
- carpeta `docs/evidencias/` (si piden pruebas)
- `README.md`

Opcional (muy pro):
- exportar a PDF con Pandoc (cuando acabes)

---

## Próximo paso
Si quieres, el siguiente archivo que generamos es:

- `docs/guia/00-indice-guia.md` (índice navegable y bonito)
