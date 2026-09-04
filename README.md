# Conpot – Instalacion en Ubuntu Server

## Requisitos Previos Generales
-	Sistema Operativo Base: Ubuntu Server 24.04 LTS (instalación limpia).
-	Acceso: Usuario no-root con privilegios sudo.
-	Red: Conexión a Internet para descargar dependencias e imágenes de contenedor.

## Despliegue de Conpot ICS/SCADA Honeypot en Ubuntu Server 24.04 LTS

Este repositorio contiene la guía paso a paso para desplegar Conpot, un honeypot de baja interacción diseñado para simular sistemas de control industrial (ICS/SCADA), en una instalación limpia de Ubuntu Server 24.04 LTS.

Se detallan dos metodologías de despliegue:
1. Despliegue en Contenedor (Docker): Método recomendado para un entorno aislado y portable.
2. Despliegue Nativo (Entorno Virtual de Python): Método para entornos de pruebas directos o investigación.

## Prerequisitos y Preparación del Host
Antes de proceder con cualquiera de los dos métodos, actualice el sistema e instale los paquetes de soporte esenciales:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git build-essential libxslt1-dev libxml2-dev libffi-dev libssl-dev libmempool-dev libpcap-dev
```


## Opción 1: Despliegue mediante Docker (Recomendado)
El uso de Docker aísla el honeypot del sistema operativo host, facilitando la limpieza y evitando la alteración de dependencias globales.

Paso 1: Instalación de Docker y Docker Compose
```
> Instalación de Docker desde los repositorios oficiales de Ubuntu
sudo apt install -y docker.io docker-compose-v2
> Habilitar e iniciar el servicio
sudo systemctl enable --now docker
> Agregar el usuario actual al grupo docker para evitar el uso de sudo
sudo usermod -aG docker $USER
newgrp Docker
```

Paso 2: Obtención del código fuente y construcción de la imagen
```
> Clonar el repositorio oficial de Conpot
git clone [https://github.com/mushorg/conpot.git](https://github.com/mushorg/conpot.git)
cd conpot
> Construir la imagen Docker localmente
docker build -t conpot:latest .
```

Paso 3: Ejecución del Contenedor
Para permitir que Conpot enlace los puertos SCADA/ICS por debajo del 1024 (ej. Modbus TCP en el puerto 502), se debe mapear la interfaz de red correctamente:
```
docker run -d \
  --name conpot_app \
  --restart always \
  -p 502:502 \
  -p 102:102 \
  -p 161:161/udp \
  -p 47808:47808/udp \
  conpot:latest
```

