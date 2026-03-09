# Evidencias del laboratorio

Esta carpeta sirve para guardar pruebas de que tu laboratorio funciona.
Piensa en ella como tu "bitacora de resultados": aqui dejas salidas de comandos y capturas.

## Para que sirve

- Demostrar que cada fase fue completada.
- Facilitar correcciones del profesor.
- Ayudarte a detectar errores si algo falla despues.

## Que guardar

Guarda dos tipos de evidencia:

1. Logs de comandos (archivos `.txt` con salida real).
2. Capturas de pantalla (que muestren comando y resultado).

## Como nombrar archivos

Usa este formato:

- `01_descripcion_corta.txt`
- `02_otra_descripcion.png`

Regla simple:

- Prefijo numerico: `01_`, `02_`, `03_`, etc.
- Descripcion corta y clara.
- Evita espacios; usa `_`.

## Carpetas por fase

Guarda cada evidencia en la carpeta correcta:

- `docs/evidencias/fase0_host/`
- `docs/evidencias/fase2_vagrant/`
- `docs/evidencias/fase3_ansible/`
- `docs/evidencias/fase4_validacion/`
- `docs/evidencias/fase5_hardening/`

## Ejemplos concretos

- `01_versions.txt`
  Comandos: `vagrant --version` y `VBoxManage --version`
- `01_vagrant_status.txt`
  Comando: `vagrant status`
- `20_ansible_ping_lab.txt`
  Comando: `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab`
- `30_curl_web_headers.txt`
  Comando: `curl -I http://192.168.56.11`
- `40_db_mariadb_active.txt`
  Comando: `systemctl is-active mariadb`

## Consejos para capturas buenas

- Muestra SIEMPRE el comando y su salida completa.
- Incluye el prompt para saber en que maquina estas (`host`, `control`, `db`).
- Evita recortar la parte importante del resultado.
- Si hay error, guardalo tambien: tambien cuenta como evidencia de diagnostico.

## Nota sobre Git

Esta carpeta esta ignorada por Git por defecto.

Eso se define en `.gitignore` con esta regla principal:

- `docs/evidencias/*`

Y con estas excepciones para mantener la documentacion base:

- `!docs/evidencias/README.md`
- `!docs/evidencias/00_template_evidencias.txt`
- `!docs/evidencias/**/`

Si quieres versionar evidencias en Git, ajusta esas lineas segun tu politica.
