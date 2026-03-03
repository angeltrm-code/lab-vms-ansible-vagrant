# Empaquetado final de la guía (cómo dejarlo “bonito” y entregable)

> Objetivo: pasar de “varios .md sueltos” a una guía organizada, fácil de navegar y lista para entregar (clase, portfolio o documentación interna).

---

## 1) Qué vamos a empaquetar

Vamos a unificar estos documentos en una estructura clara:

- `vagrant-y-ansible-explicacion.md`
- `hoja-de-ruta-lab-vagrant-ansible.md`
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

Dentro de tu proyecto `lab-vagrant-ansible/`:

```
lab-vagrant-ansible/
├─ Vagrantfile
├─ ansible/
├─ docs/
│  ├─ guia/
│  │  ├─ 00_indice.md
│  │  ├─ 00_conceptos_vagrant_ansible.md
│  │  ├─ 00_hoja_de_ruta.md
│  │  ├─ 01_fase_0_host.md
│  │  ├─ 02_fase_1_diseno.md
│  │  ├─ 03_fase_2_vagrant.md
│  │  ├─ 04_fase_3_ansible.md
│  │  ├─ 05_fase_4_validacion.md
│  │  └─ 06_fase_5_hardening.md
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
| `vagrant-y-ansible-explicacion.md` | `docs/guia/00_conceptos_vagrant_ansible.md` |
| `hoja-de-ruta-lab-vagrant-ansible.md` | `docs/guia/00_hoja_de_ruta.md` |
| `fase-0-preparacion-host.md` | `docs/guia/01_fase_0_host.md` |
| `fase-1-diseno-del-laboratorio.md` | `docs/guia/02_fase_1_diseno.md` |
| `fase-2-provisionado-vagrant-virtualbox.md` | `docs/guia/03_fase_2_vagrant.md` |
| `fase-3-ansible-automatizacion.md` | `docs/guia/04_fase_3_ansible.md` |
| `fase-4-validacion-evidencias.md` | `docs/guia/05_fase_4_validacion.md` |
| `fase-5-troubleshooting-hardening.md` | `docs/guia/06_fase_5_hardening.md` |

> En Windows, si no quieres usar comandos, puedes hacerlo arrastrando con el explorador.

---

## 4) Crear un índice dentro de `docs/guia/` (00_indice.md)

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

- `docs/guia/00_indice.md` (índice navegable y bonito)
