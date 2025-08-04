# Traefik v2 con Let's Encrypt

Este repositorio proporciona una configuración básica para usar **Traefik v2** como un proxy inverso dinámico para tus contenedores Docker, incluyendo la gestión automática de certificados SSL/TLS con **Let's Encrypt**.

Traefik simplifica la exposición de tus servicios Docker a la web, manejando el enrutamiento y la seguridad de forma automática.

---

**¡NOTA IMPORTANTE!**
Para que Traefik pueda enrutar correctamente el tráfico a tus servicios, es **CRÍTICO** que el contenedor de Traefik y **todos los contenedores que deseas exponer** estén conectados a la **misma red Docker**. Si no lo están, es muy probable que encuentres errores de "Bad Gateway". ¡Ten esto siempre en cuenta al desplegar tus stacks!

---

## Configuración Inicial

Antes de lanzar Traefik, necesitas crear un archivo para que Let's Encrypt almacene los certificados y asegurarte de que tenga los permisos correctos.

1.  **Crear el archivo `acme.json`:**

    ```bash
    touch acme.json
    ```

2.  **Establecer permisos seguros para `acme.json`:**
    Esto es crucial para la seguridad, ya que `acme.json` contendrá tus certificados.

    ```bash
    chmod 600 acme.json
    ```

---

## Opciones de Despliegue

Puedes desplegar Traefik usando `docker-compose` para una configuración más organizada, o directamente con `docker run` para un control más granular.

### Opción 1: Despliegue con `docker-compose` (Recomendado)

Esta es la forma más sencilla de levantar Traefik junto con otros servicios.

1.  **Crear una red Docker compartida:**
    Esta red será utilizada por Traefik y tus otros contenedores para comunicarse.

    ```bash
    docker network create web
    ```

2.  **Iniciar Traefik y tus servicios definidos en `docker-compose.yml`:**
    Asegúrate de tener un archivo `docker-compose.yml` configurado para Traefik y tus aplicaciones.

    ```bash
    docker-compose up -d
    ```

### Opción 2: Despliegue con `docker run`

Si prefieres un control más directo o no usas `docker-compose` para este servicio, puedes usar `docker run`.

1.  **Crear una red Docker compartida:**

    ```bash
    docker network create web
    ```

2.  **Ejecutar el contenedor de Traefik:**
    Este comando monta los archivos de configuración y el socket de Docker, y expone los puertos HTTP (80) y HTTPS (443).

    ```bash
    docker run -d \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v $PWD/traefik.toml:/traefik.toml \
      -v $PWD/traefik_dynamic.toml:/traefik_dynamic.toml \
      -v $PWD/acme.json:/acme.json \
      -p 80:80 \
      -p 443:443 \
      --network web \
      --name traefik \
      traefik:v2.2
    ```

    * `-v /var/run/docker.sock:/var/run/docker.sock`: Permite a Traefik comunicarse con el demonio Docker para descubrir servicios.
    * `-v $PWD/traefik.toml:/traefik.toml`: Monta el archivo de configuración principal de Traefik.
    * `-v $PWD/traefik_dynamic.toml:/traefik_dynamic.toml`: Monta un archivo para configuraciones dinámicas.
    * `-v $PWD/acme.json:/acme.json`: Monta el archivo donde Let's Encrypt almacenará los certificados.
    * `-p 80:80`: Mapea el puerto HTTP 80 del host al puerto 80 del contenedor.
    * `-p 443:443`: Mapea el puerto HTTPS 443 del host al puerto 443 del contenedor.
    * `--network web`: Conecta Traefik a la red `web`.
    * `--name traefik`: Asigna el nombre `traefik` al contenedor.
    * `traefik:v2.2`: Especifica la imagen de Traefik a usar.

---

## Referencia

Para una guía más detallada sobre la configuración de Traefik v2 con Docker y Let's Encrypt, puedes consultar el siguiente tutorial:

* [How To Use Traefik v2 as a Reverse Proxy for Docker Containers on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-traefik-v2-as-a-reverse-proxy-for-docker-containers-on-ubuntu-20-04)

---

# Traefik v2 with Let's Encrypt (English Version)

This repository provides a basic setup to use **Traefik v2** as a dynamic reverse proxy for your Docker containers, including automatic SSL/TLS certificate management with **Let's Encrypt**.

Traefik simplifies exposing your Docker services to the web by automatically handling routing and security.

---

**IMPORTANT NOTE!**
For Traefik to correctly route traffic to your services, it is **CRITICAL** that the Traefik container and **all containers you wish to expose** are connected to the **same Docker network**. If they are not, you will very likely encounter "Bad Gateway" errors. Always keep this in mind when deploying your stacks!

---

## Initial Setup

Before launching Traefik, you need to create a file for Let's Encrypt to store certificates and ensure it has the correct permissions.

1.  **Create the `acme.json` file:**

    ```bash
    touch acme.json
    ```

2.  **Set secure permissions for `acme.json`:**
    This is crucial for security, as `acme.json` will contain your certificates.

    ```bash
    chmod 600 acme.json
    ```

---

## Deployment Options

You can deploy Traefik using `docker-compose` for a more organized setup, or directly with `docker run` for more granular control.

### Option 1: Deploy with `docker-compose` (Recommended)

This is the simplest way to bring up Traefik along with other services.

1.  **Create a shared Docker network:**
    This network will be used by Traefik and your other containers to communicate.

    ```bash
    docker network create web
    ```

2.  **Start Traefik and your services defined in `docker-compose.yml`:**
    Ensure you have a `docker-compose.yml` file configured for Traefik and your applications.

    ```bash
    docker-compose up -d
    ```

### Option 2: Deploy with `docker run`

If you prefer more direct control or are not using `docker-compose` for this service, you can use `docker run`.

1.  **Create a shared Docker network:**

    ```bash
    docker network create web
    ```

2.  **Run the Traefik container:**
    This command mounts configuration files and the Docker socket, and exposes HTTP (80) and HTTPS (443) ports.

    ```bash
    docker run -d \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v $PWD/traefik.toml:/traefik.toml \
      -v $PWD/traefik_dynamic.toml:/traefik_dynamic.toml \
      -v $PWD/acme.json:/acme.json \
      -p 80:80 \
      -p 443:443 \
      --network web \
      --name traefik \
      traefik:v2.2
    ```

    * `-v /var/run/docker.sock:/var/run/docker.sock`: Allows Traefik to communicate with the Docker daemon to discover services.
    * `-v $PWD/traefik.toml:/traefik.toml`: Mounts the main Traefik configuration file.
    * `-v $PWD/traefik_dynamic.toml:/traefik_dynamic.toml`: Mounts a file for dynamic configurations.
    * `-v $PWD/acme.json:/acme.json`: Mounts the file where Let's Encrypt will store certificates.
    * `-p 80:80`: Maps host HTTP port 80 to container port 80.
    * `-p 443:443`: Maps host HTTPS port 443 to container port 443.
    * `--network web`: Connects Traefik to the `web` network.
    * `--name traefik`: Assigns the name `traefik` to the container.
    * `traefik:v2.2`: Specifies the Traefik image to use.

---

## Reference

For a more detailed guide on configuring Traefik v2 with Docker and Let's Encrypt, you can consult the following tutorial:

* [How To Use Traefik v2 as a Reverse Proxy for Docker Containers on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-traefik-v2-as-a-reverse-proxy-for-docker-containers-on-ubuntu-20-04)
