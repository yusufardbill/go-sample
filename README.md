# Go Sample API

A simple Go HTTP API with health check endpoints for learning Red Hat OpenShift deployment.

## Features

- Simple HTTP API with health check endpoints
- Container-ready with multi-stage Dockerfile
- OpenShift/Kubernetes compatible health checks
- Configurable port via environment variable

## Endpoints

- `GET /` - Welcome message
- `GET /health` - Health check endpoint
- `GET /healthz` - Kubernetes/OpenShift style health check

### Health Check Response

```json
{
  "status": "healthy",
  "timestamp": "2026-01-05T04:01:36Z",
  "service": "go-sample"
}
```

## Local Development

### Run directly with Go

```bash
go run main.go
```

The server will start on port 8080 by default.

### Test the endpoints

```bash
# Test root endpoint
curl http://localhost:8080/

# Test health check
curl http://localhost:8080/health
```

## Docker

### Build the image

```bash
docker build -t go-sample:latest .
```

### Run the container

```bash
docker run -p 8080:8080 go-sample:latest
```

## OpenShift Deployment

### Option 1: Deploy from source (S2I)

```bash
oc new-app golang:1.25~https://github.com/yourusername/go-sample.git
oc expose svc/go-sample
```

### Option 2: Deploy from Dockerfile

```bash
oc new-app . --name=go-sample
oc expose svc/go-sample
```

### Option 3: Deploy from local image

```bash
# Build and push to OpenShift internal registry
docker build -t go-sample:latest .
docker tag go-sample:latest image-registry.openshift-image-registry.svc:5000/yourproject/go-sample:latest
docker push image-registry.openshift-image-registry.svc:5000/yourproject/go-sample:latest

# Create deployment
oc new-app yourproject/go-sample:latest
oc expose svc/go-sample
```

### Check deployment status

```bash
oc get pods
oc get routes
```

### Access the application

```bash
# Get the route URL
ROUTE=$(oc get route go-sample -o jsonpath='{.spec.host}')

# Test the health check
curl http://$ROUTE/health
```

## Environment Variables

- `PORT` - HTTP port (default: 8080)

## OpenShift Configuration

The application is configured to work with OpenShift's requirements:

- Uses port 8080 by default (configurable via PORT env var)
- Includes both `/health` and `/healthz` endpoints for probes
- Multi-stage Docker build for smaller image size
- Non-root user compatible
