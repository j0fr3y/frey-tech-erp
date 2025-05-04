
# Frey-Tech ERP Update Process

This document outlines the steps to update the Frey-Tech ERP system.

## Step 1: Encode `apps.json` to Base64

Run the following command to encode your `apps.json` file into a Base64 string:

```bash
export APPS_JSON_BASE64=$(base64 -w 0 /path/to/apps.json)
```

Replace `/path/to/apps.json` with the actual path to your `apps.json` file.

## Step 2: Build and Push the Docker Image

Use the following command to build and push the Docker image for the Frey-Tech ERP system:

```bash
docker buildx build \
    --platform=linux/amd64 \
    --build-arg FRAPPE_PATH=https://github.com/frappe/frappe \
    --build-arg FRAPPE_BRANCH=version-15 \
    --build-arg APPS_JSON_BASE64=$APPS_JSON_BASE64 \
    --tag ghcr.io/j0fr3y/frey-tech-erp/frey-tech-erp-dev:1.0.1 \
    --tag ghcr.io/j0fr3y/frey-tech-erp/frey-tech-erp-dev:latest \
    --file images/layered/Containerfile \
    --push \
    .
```

This command builds the Docker image using the specified arguments and pushes it to the GitHub Container Registry.

## Step 3: Migrate site

Note:

- Wait for the `db` service to start and `configurator` to exit before trying to migrate a site. Usually this takes up to 10 seconds.

```sh
docker compose exec backend bench --site dev-erp.home.frey-tech.de migrate
```