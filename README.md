# ¿Cómo crear ambientes de desarrollo para tus equipos con Ansible Automation Platform?

## El Problema
- Tenemos equipos que no tienen RHEL ni Linux en sus equipos de escritorio.
- Queremos que estos equipos desarrollen código de Ansible y puedan probarlo a través de la línea de comando.
- Queremos usar las versiones de las herramientas de CLI soportadas por AAP: `ansible-navigator`, `ansible-lint`, `ansible-builder` y `molecule`.
- Queremos usar VSCode y la extensión oficial de Red Hat para VSCode, con sintaxis, autocompletado y análisis estático (linting).

## La Solución
1. Utilizar un solo servidor RHEL donde los miembros del equipo tengan cuentas locales o asociadas a un login centralizado. 
2. Registrar ese servidor a los repositorios de Ansible Automation Platform (AAP) e instalar todas las versiones oficiales de las herramientas CLI.
3. Instalar la extensión SSH para VSCode para poder trabajar en el servidor como si estuviésemos localmente
4. Instalar y configurar la extensión de Ansible para automatizar el linting, el language server con el autocompletado, usar los ambientes de ejecución y todos los features que de otra forma sólo funcionarían en un desktop Linux.


## 1. Servidor RHEL 

Debemos provisionar un servidor estándar corriendo Red Hat Enterprise Linux 9. Si el sistema vive detrás de un servidor Satellite, debemos asegurarnos que la suscripciones de Ansible Automation Platform estén incluidas.

> [!NOTE]
> Con RHEL9 y las suscripciones de Ansible Automation Platform, nos aseguramos que las versiones de las herramientas están soportadas y que serán las mismas a usar en producción. No debemos usar pip ni otros paquetes comunitarios.

> [!NOTE]
> La suscripción de Ansible Automation Platform asociada a la __Red Hat Developer Subscription__ nos da acceso permanente a estas herramientas también. Ver http://developers.redhat.com y https://www.redhat.com/en/technologies/management/ansible/trial

## 2. Paquetes CLI de Ansible Automation Platform

Todas las herramientas de CLI vienen en un metapaquete llamado `ansible-dev-tools`. 

1. Nos aseguramos de que está instalado el repositorio que necesitamos:

```sh
$ sudo yum search ansible-dev-tools --enablerepo=ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms
Updating Subscription Management repositories.
Last metadata expiration check: 0:11:17 ago on Mon May 19 23:44:19 2025.
======================================================== Name Exactly Matched: ansible-dev-tools =========================================================
ansible-dev-tools.noarch : Ansible Developtment Tools kit bundles all tools needed for content creation and testing
======================================================= Name & Summary Matched: ansible-dev-tools ========================================================
ansible-dev-tools+server.noarch : Metapackage for ansible-dev-tools: server extra
```

2. Luego instalamos todas las herramientas con el metapaquete `ansible-dev-tools`:

```sh
$ sudo yum install ansible-dev-tools --enablerepo=ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms
Updating Subscription Management repositories.
Last metadata expiration check: 0:03:46 ago on Mon May 19 23:44:19 2025.
Dependencies resolved.
==========================================================================================================================================================
 Package                                     Architecture  Version                    Repository                                                     Size
==========================================================================================================================================================
Installing:
 ansible-dev-tools                           noarch        25.2.0-1.el9ap             ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms         49 k
 ...
 ...
```

3. Confirmar que tenemos todas las herramientas que nos interesan. En este caso son: `ansible-navigator`, `ansible-lint`, `ansible-builder` y `molecule`. (Todas las versiones que se muestran acá corresponden a la versión 2.5 de AAP)

```sh
$ ansible-navigator --version
ansible-navigator 25.1.0

$ ansible-lint --version
ansible-lint 25.1.2 using ansible-core:2.16.14 ansible-compat:25.1.2 ruamel-yaml:0.18.6 ruamel-yaml-clib:0.2.8

$ ansible-builder --version
3.1.0

$ molecule --version
molecule 25.2.0 using python 3.11 
    ansible:2.16.14
    default:25.2.0 from molecule

```

## 3. Extensión SSH para VSCode
Pensando en que los desarrolladores de automatización no están en un escritorio de RHEL, existe una extensión de Visual Studio Code (VSCode) para trabajar en un servidor SSH simulando tenerlo localmente: [Visual Studio Code Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). Usa las instrucciones del link para instalarla y hacer `Connect to Host...`a nuestro servidor RHEL.

