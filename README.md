# Lab VMs + VirtualBox + Vagrant + Ansible (Guía completa)

> Guía didáctica paso a paso para público sin experiencia previa.  
> Objetivo: levantar un laboratorio reproducible de 3 máquinas en VirtualBox con Vagrant y automatizar su configuración con Ansible.

## Índice principal

La entrada recomendada del curso es: **[docs/guia/00-indice-guia.md](docs/guia/00-indice-guia.md)**

## Qué incluye esta guía

- Explicación de conceptos (Vagrant y Ansible) para principiantes.
- Hoja de ruta del proyecto (fases 0–5).
- Guía por fases:
  - **Fase 0**: preparación del host (tu PC).
  - **Fase 1**: diseño del laboratorio.
  - **Fase 2**: provisionado con Vagrant + VirtualBox.
  - **Fase 3**: automatización con Ansible.
  - **Fase 4**: validación y evidencias.
  - **Fase 5**: troubleshooting + hardening ligero.

## Estructura recomendada del proyecto

```text
lab-vms-ansible-vagrant/
|- Vagrantfile
|- ansible/
|  |- ansible.cfg
|  |- inventory.ini
|  |- site.yml
|  |- playbooks/
|  `- group_vars/
|- docs/
|  |- guia/
|  `- evidencias/
`- README.md
```

### Dónde guardar los `.md` de la guía

- Guía: `docs/guia/`
- Evidencias de ejecución: `docs/evidencias/`

## Orden recomendado de lectura

1. [docs/guia/00-conceptos-vagrant-ansible.md](docs/guia/00-conceptos-vagrant-ansible.md)
2. [docs/guia/00-hoja-de-ruta.md](docs/guia/00-hoja-de-ruta.md)
3. [docs/guia/01-fase-0-host.md](docs/guia/01-fase-0-host.md)
4. [docs/guia/02-fase-1-diseno.md](docs/guia/02-fase-1-diseno.md)
5. [docs/guia/03-fase-2-vagrant.md](docs/guia/03-fase-2-vagrant.md)
6. [docs/guia/04-fase-3-ansible.md](docs/guia/04-fase-3-ansible.md)
7. [docs/guia/05-fase-4-validacion.md](docs/guia/05-fase-4-validacion.md)
8. [docs/guia/06-fase-5-hardening.md](docs/guia/06-fase-5-hardening.md)

## Ejecución rápida (curso)

1. Instala VirtualBox y Vagrant, y verifica con `vagrant --version` y `VBoxManage --version`.
2. En la raíz del repo, ejecuta `vagrant up` para levantar `control`, `web` y `db`.
3. Entra a `control` con `vagrant ssh control` e instala Ansible (`sudo apt update && sudo apt install -y ansible openssh-client`).
4. Genera/copia la clave SSH desde `control` hacia `web` y `db` según la guía de Fase 3.
5. En `control`, ve a `/vagrant/ansible` y valida conectividad: `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab`.
6. Ejecuta `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible-playbook site.yml` dos veces para comprobar idempotencia.
7. Verifica servicios: `curl http://192.168.56.11` y `systemctl is-active mariadb` en `db`.

Nota: en `/vagrant/ansible` usamos `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg` porque Ansible puede ignorar `ansible.cfg` en directorios world-writable.

## Checklist final de lab funcional

- [ ] `vagrant status` muestra VMs en `running`.
- [ ] `vagrant ssh control` funciona.
- [ ] `ANSIBLE_CONFIG=/vagrant/ansible/ansible.cfg ansible -m ping lab` devuelve `SUCCESS`.
- [ ] `curl http://192.168.56.11` responde con la página de prueba.
- [ ] `systemctl is-active mariadb` en `db` devuelve `active`.
- [ ] Evidencias guardadas en `docs/evidencias/`.

## Notas y buenas prácticas

- Mantén IPs fijas en Host-Only para que Ansible sea estable.
- Si algo falla, usa la Fase 5 para diagnóstico.
- En una segunda ejecución del playbook deberían aparecer pocos `changed`.

## Próximas ampliaciones

- Añadir VM `monitor`.
- Convertir playbooks en roles Ansible.
- Templates con Jinja2.
- Separar variables por entorno (`dev`/`test`).

## Backups

Los backups se guardan en `_bak/` y no forman parte de la guía.
La carpeta `_bak/` replica la ruta original para mantener trazabilidad (por ejemplo: `_bak/docs/guia/...`).
