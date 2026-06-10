# Hostinger VPS: Local Container Registry Setup Guide

To deploy a secure local container registry on your Hostinger VPS, we will use the official Docker `registry:2` image and expose it through your existing Traefik reverse proxy to ensure it has an SSL/TLS certificate (HTTPS is required by Docker to push/pull images securely).

## Prerequisites
1. **Domain Name**: A subdomain pointing to your Hostinger VPS (e.g., `registry.tmkcomputers.in`).
2. **htpasswd utility**: Needed to generate basic authentication credentials to secure your registry.

## Step 1: Generate Authentication Credentials

You must secure your registry so unauthorized users cannot push or pull images. We use `htpasswd` for this.

On your VPS (or locally, if you have `htpasswd` installed), generate a credentials file:

```bash
# Install apache2-utils if you don't have htpasswd
sudo apt-get install apache2-utils

# Create an auth directory
mkdir -p /home/tmk/registry/auth

# Generate the htpasswd file (replace 'admin' with your desired username)
htpasswd -Bc /home/tmk/registry/auth/htpasswd admin
# You will be prompted to enter a password twice. Keep this password safe!
```

## Step 2: Create the `docker-compose.yml` (With UI Dashboard)

We will deploy both the Registry and the Visual UI (`joxit/docker-registry-ui`). 

> [!WARNING]
> **Ultimate Single-Domain Setup**:
> This configuration uses the standard `joxit/docker-registry-ui` image instead of the `static` tag. By passing `NGINX_PROXY_PASS_URL`, the UI container acts as a reverse proxy for your registry API. 
> - **No CORS needed.**
> - **Only one domain needed (`registry.daspdigital.com`).**
> - The browser requests the UI, and the UI securely forwards API calls internally to the registry.

Create your `docker-compose.yml`:

```yaml
version: '3.9'

services:
  # 1. The actual Docker Registry API
  registry:
    image: registry:2
    container_name: local-registry
    restart: always
    environment:
      REGISTRY_STORAGE_DELETE_ENABLED: "true"
      REGISTRY_CATALOG_MAXENTRIES: 100000
      
      # Basic Auth (Required to secure the registry)
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: "Registry Realm"
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      # Notice: CORS headers are completely removed! We don't need them anymore.
    volumes:
      - ./data:/var/lib/registry
      - /home/tmk/registry/auth:/auth # Use your absolute path here
    networks:
      - traefik_net
    # Notice: All Traefik labels are removed from the registry container!
    # The UI container handles all public traffic and forwards it internally.

  # 2. The Visual UI Dashboard (Acting as the Public Gateway)
  registry-ui:
    image: joxit/docker-registry-ui # Removed ":static"
    container_name: local-registry-ui
    restart: always
    environment:
      - SINGLE_REGISTRY=true
      - REGISTRY_TITLE=DevOps Private Registry
      - DELETE_IMAGES=true
      - SHOW_CONTENT=true
      # This is the magic! The UI proxies /v2/ API traffic internally to the registry container
      - NGINX_PROXY_PASS_URL=http://registry:5000
    labels:
      - "traefik.enable=true"
      # Serve both the UI and the Registry API on this single domain
      - "traefik.http.routers.registryui.rule=Host(`registry.daspdigital.com`)"
      - "traefik.http.routers.registryui.entrypoints=websecure"
      - "traefik.http.routers.registryui.tls.certresolver=myresolver"
      - "traefik.http.services.registryui.loadbalancer.server.port=80"
    networks:
      - traefik_net

networks:
  traefik_net:
    external: true
```

## Step 3: Deploy the Registry

Run the following command in the directory containing your `docker-compose.yml`:

```bash
docker compose up -d
```

Traefik will automatically detect the new container, route traffic for `registry.daspdigital.com` to it, and generate a Let's Encrypt SSL certificate.

## Step 4: Verify and Login

Before you can push or pull images, you need to authenticate your Docker client against your newly created registry.

On any machine (or the VPS itself) that will interact with the registry, run:

```bash
docker login registry.daspdigital.com
```
*Enter the username (`admin`) and the password you set in Step 1.*

## Step 5: Pushing and Pulling Images

To push an image to your new private registry, you must tag it with the registry's domain:

**1. Build your image locally:**
```bash
docker build -t sales-booster-api:latest .
```

**2. Tag the image for your registry:**
```bash
docker tag sales-booster-api:latest registry.daspdigital.com/sales-booster-api:latest
```

**3. Push the image:**
```bash
docker push registry.daspdigital.com/sales-booster-api:latest
```

**4. Pull the image (on the production host):**
```bash
# Ensure the host is logged in via `docker login` first
docker pull registry.daspdigital.com/sales-booster-api:latest
```

> [!TIP]
> **Integration with DevOps Manager**: 
> Once your registry is live, your CI/CD pipeline (e.g., GitHub Actions) can build the image and push it to `registry.daspdigital.com`. Your DevOps Manager's `DeployService.cs` can then be configured to run `docker pull registry.daspdigital.com/project-name:latest` instead of building the image on the host directly!
