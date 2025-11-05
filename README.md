# Guía básica de virtualización con Proxmox VE

## 1. Acceso a la interfaz web de Proxmox VE

Usaremos la **interfaz web** de Proxmox VE desde un navegador (Chrome, Edge, Firefox). 

### 1.1. IP y puerto de Proxmox

El administrador te entregará:
- **IP del servidor Proxmox** (ej.: `172.25.205.161`)
- **Puerto de administración** (**por defecto `8006`**)

Luego, abre el navegador y accede con tus credenciales que te asignaron:

```text
https://IP_DEL_PROXMOX:8006
```

![Pantalla de login de Proxmox](images/paso1.png)

## 2. Preparar imágenes para las máquinas virtuales y contenedores

Antes de crear una VM o un contenedor LXC, Proxmox necesita tener:

- **Imágenes ISO** de sistemas operativos (para las VMs).
- **Plantillas de contenedor (CT Templates)** para los LXC.

### Paso 2.1: Cargar imágenes ISO para las VM

En el panel izquierdo selecciona el almacenamiento del nodo, por ejemplo:

- `local (proxmox-test-1)`

Luego, en la parte central, abre la pestaña **ISO Images**. Arriba verás dos botones principales:

- **Upload**  
  Permite **subir un archivo `.iso` desde tu computadora** al almacenamiento `local`.  
  Úsalo cuando ya descargaste la ISO (por ejemplo, `ubuntu-22.04.iso`) en tu PC y quieres tenerla disponible en Proxmox.

- **Download from URL**  
  Permite **descargar una ISO directamente desde una URL**.  
  Es útil si el servidor Proxmox tiene acceso a internet y quieres bajar la ISO desde el sitio oficial sin pasar por tu PC.

![Gestión de imágenes ISO en Proxmox](images/paso3.png)

Todas las ISOs que cargues aquí aparecerán en la lista y luego podrás seleccionarlas al usar el asistente **Create VM**.

### Paso 2.2: Descargar o subir CT Templates (contenedores LXC)

En el mismo almacenamiento `local (proxmox-test-1)`, selecciona ahora la pestaña **CT Templates**. Aquí gestionas las **plantillas que se usan para crear contenedores LXC**.

En la parte superior verás:

- **Templates**  
  Abre un catálogo de plantillas oficiales (Debian, Ubuntu, Alpine, etc.) de los repositorios de Proxmox.  
  Desde aquí puedes elegir una distribución y descargarla con un clic.

- **Upload**  
  Permite **subir una plantilla de contenedor** (`.tar.xz`, `.tar.zst`) que ya tengas descargada en tu PC.

- **Download from URL**  
  Permite **descargar una plantilla desde una URL directa**, similar a las ISO.

![Gestión de CT Templates en Proxmox](images/paso4.png)

Una vez que tengas al menos una plantilla descargada, podrás seleccionarla cuando uses el asistente **Create CT** para crear contenedores LXC ligeros.

## 4. Crear máquinas virtuales (VM) y contenedores (CT) desde la interfaz

Proxmox VE permite dos tipos principales de entornos en el nodo:

- **Máquinas virtuales (VM)**: sistemas completos (Linux, Windows, etc.) con su propio hardware virtual (CPU, RAM, disco).
- **Contenedores LXC (CT)**: sistemas Linux ligeros que comparten el kernel del host y consumen menos recursos.

> 💡 Antes de empezar, selecciona tu nodo (por ejemplo, `proxmox-test-1`) en el panel izquierdo.

![Botones Create VM y Create CT](images/paso2.png)

Con el nodo seleccionado, en la parte superior derecha verás dos botones:

- **Create VM**  
  Abre el asistente para crear una VM a partir de una imagen ISO. En los siguientes pasos elegirás ISO, tamaño de disco, CPU, RAM y red.

- **Create CT**  
  Abre el asistente para crear un contenedor LXC usando una plantilla de CT (de la sección **CT Templates**). También configurarás nombre, disco, CPU, RAM y red.

En las siguientes secciones de la guía se explicará paso a paso cómo:

1. Crear la **primera VM** con **Create VM**.  
2. Crear el **primer contenedor LXC** con **Create CT**.  
3. Más adelante, ver una alternativa usando **scripts desde la pestaña Shell** del nodo.

## 5. Crear la primera máquina virtual (VM) con el asistente

En este paso vamos a crear una VM de prueba usando el botón **Create VM**.

![Asistente de creación de VM en Proxmox](images/paso5.png)

El asistente tiene varias pestañas. Para una configuración básica de laboratorio puedes usar estos valores:

### 5.1. General

- **Node:** se selecciona automáticamente (ej. `ciber`).
- **VM ID:** Proxmox propone uno libre; puedes dejarlo por defecto.
- **Name:** escribe un nombre descriptivo, por ejemplo `prueba`, `kali`, `win10-lab`.

Pulsa **Next**.

### 5.2. OS (sistema operativo)

- Marca **Use CD/DVD disc image file (iso)**.
- **Storage:** el almacenamiento donde subiste las ISOs (ej. `local`).
- **ISO image:** selecciona la ISO que cargaste en el *Paso 3* (ej. `kali-linux-2025.3-installer-amd64.iso`).
- **Guest OS:** selecciona el tipo (`Linux` / `Windows`) y versión aproximada.

Pulsa **Next**.

### 5.3. System

- Deja las opciones por defecto para una instalación básica.
- **Qemu Agent:** recomendado **marcarlo** para poder usar las funciones avanzadas de Proxmox cuando instales el agente dentro de la VM.

Pulsa **Next**.

### 5.4. Disks

- **Bus/Device:** `SCSI` (por defecto está bien).
- **Storage:** `local` (o el almacenamiento que uses).
- **Disk size (GiB):** por ejemplo `32` GB para un sistema de pruebas.
- **Format:** `QEMU image format (qcow2)` suele ser una buena opción para laboratorios.

Pulsa **Next**.

### 5.5. CPU

- **Sockets:** `1`.
- **Cores:** por ejemplo `2` (o más si tu servidor lo permite).
- **Type:** puedes usar **`host`** para aprovechar al máximo la CPU real, pero puedes elegir kvm64.

Pulsa **Next**.

### 5.6. Memory

- **Memory (MiB):** elige la RAM para la VM, por ejemplo `4096` (4 GB) para un servidor de pruebas. Ajusta según los recursos del host y el sistema invitado.

Pulsa **Next**.

### 5.7. Network

- **Bridge:** normalmente `vmbr0` (la interfaz de puente principal del host).
- **Model:** `VirtIO (paravirtualized)` para mejor rendimiento.
- **Firewall:** puedes dejarlo marcado si planeas usar las reglas de firewall de Proxmox más adelante.

Pulsa **Next**.

### 5.8. Confirm

En la pestaña **Confirm** verás un resumen de todos los parámetros:

- Nombre, VMID, ISO seleccionada.
- CPU, RAM y disco asignados.
- Configuración de red.

Opcionalmente puedes marcar **Start after created** para encender la VM al terminar.

Haz clic en **Finish** para que Proxmox cree la VM.

---

Con esto ya tienes una **VM básica creada y lista para instalar el sistema operativo** desde la ISO, te tocará configurar tu VM, solo seguir el wizard de instalación.

![Finalización de Kali linux en Proxmox](images/paso6.png)

