# PhotoCraft Web — Docker Image

[![Build and publish PhotoCraft Web](https://github.com/MikaPST/photocraft-web/actions/workflows/publish-image.yml/badge.svg)](https://github.com/MikaPST/photocraft-web/actions/workflows/publish-image.yml)
[![Docker Image](https://ghcr-badge.egpl.dev/mikapst/photocraft-web/latest_tag?trim=major&label=GHCR)](https://github.com/MikaPST/photocraft-web/pkgs/container/photocraft-web)

A community-maintained Docker image for **[PhotoCraft](https://github.com/storytold/photocraft)**, providing its browser-based image editor through a self-hosted web interface.

This repository automates the build and publication of the Docker image to GitHub Container Registry (GHCR), making PhotoCraft Web easier to deploy on Unraid and other Docker-compatible platforms.

## Why this repository?

The official PhotoCraft project provides a WebAssembly-based version that can be hosted as a static website. This repository packages the web application into a Docker image to simplify self-hosting.

The goals are to:

- Build the PhotoCraft Web application from the upstream source code.
- Publish a ready-to-run Docker image to GHCR.
- Automatically rebuild the image every week to incorporate upstream changes.
- Provide a convenient deployment option for Unraid Community Applications.
- Keep the packaging and deployment configuration separate from the upstream PhotoCraft project.

This is a packaging and distribution project, not a fork intended to develop a separate image editor.

## How it works

PhotoCraft Web runs directly in the user's browser using WebAssembly. The Docker container serves the static web application through NGINX; it does not provide a remote Linux desktop.

- **Browser-based interface:** access the editor through a web browser.
- **Local file selection:** opening a file uses the browser's file picker.
- **Browser downloads:** saving and exporting documents uses the browser's download mechanism.
- **No remote desktop:** no VNC, remote desktop session or Linux graphical environment is required.
- **Self-hosted:** run the application on your own server and access it over your local network or through a properly configured reverse proxy.

File selection, editing and export are handled by the browser application. The container serves the application files rather than acting as a document-storage service.

## Docker image

The image is published to GitHub Container Registry:

```text
ghcr.io/mikapst/photocraft-web:latest
```

### Quick start

```bash
docker pull ghcr.io/mikapst/photocraft-web:latest

docker run -d \
  --name photocraft-web \
  --restart unless-stopped \
  --cpus=2 \
  --memory=2g \
  -p 8080:8080 \
  ghcr.io/mikapst/photocraft-web:latest
```

Open the application in your browser:

```text
http://YOUR_SERVER_IP:8080
```

Replace `YOUR_SERVER_IP` with the IP address of your server.

The container listens for HTTP traffic on port `8080`. For external access, use a reverse proxy such as Traefik to terminate HTTPS and forward requests to the container over HTTP.

The CPU and memory limits in the example are optional and can be adjusted to suit your server.

## Automated builds

GitHub Actions builds and publishes the image automatically.

| Trigger | Behaviour |
|---|---|
| Weekly schedule | Rebuilds from the upstream `main` branch |
| Manual workflow dispatch | Allows a build to be triggered manually |
| Workflow changes on `main` | Triggers a build when the workflow file changes |

Each successful build publishes:

- `latest` — the most recently published image.
- `sha-<commit>` — a build-specific tag for identifying an image.

The build targets `linux/amd64` and `linux/arm64`. Actual compatibility depends on the upstream build and the target platform.

**Important:** a weekly build does not guarantee that upstream changes are compatible or stable. PhotoCraft is under active development and is currently described by its maintainers as an early-alpha project. Test new images before relying on them for important work.

The `latest` tag is mutable and may point to a newer image after a successful build.

## Unraid Community Applications

This image is intended to be distributed through the maintainer's existing Unraid Community Applications repository.

The Unraid template will reference the published GHCR image and provide configuration for the web port and resource limits.

This repository contains the image build and publication workflow. The Unraid Community Applications template is maintained separately.

## Upstream project

PhotoCraft is an open-source image editor developed by the ArtCraft Team and its contributors.

- **Source code:** [storytold/photocraft](https://github.com/storytold/photocraft)
- **Official website:** [getartcraft.com/apps/photocraft](https://getartcraft.com/apps/photocraft)
- **Official web hosting guide:** [Docker web hosting](https://github.com/storytold/photocraft/blob/main/packaging/web/README.md)
- **Official releases:** [PhotoCraft releases](https://github.com/storytold/photocraft/releases)

This repository builds the Docker image using the upstream project's Dockerfile and packaging files. The Docker packaging recipe is community-maintained upstream and may not always match the latest official web release.

## License and attribution

PhotoCraft is dual-licensed under the **MIT License or Apache License 2.0**, at the user's option. Copyright belongs to the ArtCraft Team and PhotoCraft contributors.

The upstream project includes license and attribution information for the application and bundled third-party assets. These notices and the applicable license files must be preserved when redistributing the application.

- [Upstream MIT License](https://github.com/storytold/photocraft/blob/main/LICENSE-MIT)
- [Upstream Apache License 2.0](https://github.com/storytold/photocraft/blob/main/LICENSE-APACHE)
- [Upstream NOTICE](https://github.com/storytold/photocraft/blob/main/NOTICE)
- [Upstream asset attributions](https://github.com/storytold/photocraft/blob/main/ATTRIBUTION.md)

The licenses for the upstream application do not automatically determine the license of the packaging files maintained in this repository. Refer to the repository's own `LICENSE` file for the terms applicable to those files.

The PhotoCraft name, ArtCraft branding and logos remain subject to their respective trademark and usage terms. This repository is an independent community-maintained packaging project and is **not an official PhotoCraft release** and is not affiliated with or endorsed by the PhotoCraft or ArtCraft maintainers.

## Disclaimer

PhotoCraft is under active development. Features, compatibility and behaviour may change between builds. Use the application and its generated images and documents at your own discretion.
