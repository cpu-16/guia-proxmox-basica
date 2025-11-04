# Guía básica de virtualización con Proxmox VE

## 1. Acceso a la interfaz web de Proxmox VE

Para administrar Proxmox VE usaremos su **interfaz web**, accesible desde un navegador moderno (Chrome, Edge, Firefox, etc.). :contentReference[oaicite:0]{index=0}  

### 1.1. IP, puerto y Tailscale

Antes de empezar, el administrador del laboratorio te entregará:

- **IP del servidor Proxmox** (ejemplo: `172.25.205.161`)
- **Puerto de administración** (por defecto: `8006`) :contentReference[oaicite:1]{index=1}  
- Acceso a la red mediante **Tailscale**, para que puedas llegar a esa IP aunque el servidor esté en otra red.

Una vez conectado a Tailscale en tu equipo:

1. Abre el navegador.
2. Escribe en la barra de direcciones:

   ```text
   https://IP_DEL_PROXMOX:8006

