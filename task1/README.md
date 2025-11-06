# Task 1: Docker Container

## Overview

This task implements a simple web application container that responds with "Hello World" to HTTP requests on port 8080. The solution uses **nginx** as a web server with a custom configuration, packaged in a Docker container for easy deployment and accessibility across the network.

## Solution Approach

Instead of developing a custom web application in TypeScript or Python, this solution leverages the official **nginx:alpine** base image with a custom configuration. This approach is:

- **Lightweight**: Uses the minimal alpine-based nginx image
- **Simple**: No need for application code, just configuration
- **Efficient**: nginx handles HTTP requests natively with minimal overhead
- **Production-ready**: nginx is a battle-tested web server

The container is orchestrated using **Docker Compose** for simplified build and deployment processes.

## Project Structure

```
task1/
├── Dockerfile              # Container image definition
├── compose.yaml            # Docker Compose configuration
├── nginx.conf             # nginx server configuration
├── templates/             # (reserved for future use)
└── README.md              # This file
```

## Files Description

### Dockerfile

The Dockerfile builds a custom nginx container:
- **Base Image**: `nginx:alpine` - lightweight nginx distribution
- **Configuration**: Copies custom `nginx.conf` to replace default nginx configuration
- **Port Exposure**: Exposes port 8080 for HTTP traffic

### compose.yaml

Docker Compose configuration that:
- Builds the custom image from the Dockerfile
- Creates and manages the `web` service
- Maps container port 8080 to host port 8080 (accessible from localhost and network)
- Automatically creates a bridge network for container communication

### nginx.conf

Custom nginx server configuration:
- **Listen Port**: 8080 (as required)
- **Response**: Returns "Hello World\n" with HTTP 200 status
- **Content Type**: `text/plain; charset=utf-8`
- **Location**: Handles all requests to root path `/`

## Usage

### Prerequisites

- Docker Engine (version 20.10 or later)
- Docker Compose (version 2.0 or later)

### Build and Run

1. **Start the service** (builds image and runs container):
```bash
docker compose up -d
```

The `-d` flag runs the container in detached mode (background).

2. **Verify the service is running**:
```bash
docker compose ps
```

3. **Test the HTTP response**:
```bash
curl http://localhost:8080
```

Expected output:
```
Hello World
```

### Access from Network

The container port 8080 is mapped to the host's port 8080, making it accessible from:
- **Localhost**: `http://localhost:8080`
- **Network**: `http://<host-ip>:8080` (where `<host-ip>` is your machine's IP address on the network)

**Note**: Ensure your firewall allows incoming connections on port 8080 if accessing from other machines.

### Stop the Service

```bash
docker compose down
```

This stops and removes the container while preserving the built image.

### Rebuild After Changes

If you modify the Dockerfile or nginx.conf:

```bash
docker compose up -d --build
```

The `--build` flag forces a rebuild of the image.

## Testing

### Basic HTTP Test

```bash
curl http://localhost:8080
```

### Check HTTP Headers

```bash
curl -v http://localhost:8080
```

### Test from Network Device

From another computer on the same network:
```bash
curl http://<host-ip>:8080
```

Replace `<host-ip>` with the IP address of the host running Docker.

## Technical Details

### Network Configuration

- Docker Compose creates a default bridge network
- Port mapping: `host:container` → `8080:8080`
- The container is accessible on all host network interfaces (0.0.0.0:8080)

### Container Details

- **Image Base**: nginx:alpine (latest)
- **Exposed Port**: 8080
- **Configuration Method**: Custom nginx.conf replaces default configuration
- **Response Format**: Plain text with newline character

### Resource Considerations

The nginx:alpine image is minimal (~25MB) and requires:
- Minimal CPU usage
- Low memory footprint (~10-20MB)
- Single worker process by default

## Design Decisions

1. **Choice of nginx over custom application**: Simpler, more efficient, and requires no runtime dependencies
2. **Alpine base image**: Significantly smaller than standard nginx image while maintaining functionality
3. **Docker Compose**: Simplifies the build and run process, especially useful for multi-container scenarios
4. **Plain text response**: Simple and lightweight, meets the "Hello world" requirement
5. **Port 8080**: As specified in the requirements, non-privileged port accessible without root

## Troubleshooting

### Port Already in Use

If port 8080 is already in use, modify the port mapping in `compose.yaml`:

```yaml
ports:
  - "8081:8080"  # Change host port to 8081
```

Then access via `http://localhost:8081`

### Container Fails to Start

Check logs:
```bash
docker compose logs web
```

### Cannot Access from Network

1. Verify the host firewall allows port 8080
2. Ensure Docker is binding to all interfaces (0.0.0.0), not just localhost
3. Check the host's IP address: `ip addr` (Linux) or `ifconfig` (macOS)

## Future Enhancements

The `templates/` directory is reserved for future use, such as:
- HTML templates for more complex responses
- Custom error pages
- Additional nginx configuration snippets
