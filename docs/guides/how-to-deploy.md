# How to deploy

Deploy **Acme App** to a staging or production environment.

## Before you deploy

- [ ] `acme config validate` passes against the target config
- [ ] Secrets are stored in your platform secret manager
- [ ] Health and readiness probes are configured
- [ ] You have a rollback path (previous image tag or release)

## Deploy with Docker Compose

```yaml title="docker-compose.yml"
services:
  acme:
    image: acme/app:1.0.0
    ports:
      - "8080:8080"
    environment:
      ACME_APP__ENV: production
      ACME_AUTH__TOKEN: ${ACME_AUTH__TOKEN}
    volumes:
      - ./acme.yaml:/app/acme.yaml:ro
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/api/v1/health"]
      interval: 30s
      timeout: 5s
      retries: 3
```

```bash
export ACME_AUTH__TOKEN="…"
docker compose up -d
```

## Deploy to Kubernetes (sketch)

```yaml title="deployment.yaml"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: acme-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: acme-app
  template:
    metadata:
      labels:
        app: acme-app
    spec:
      containers:
        - name: acme
          image: acme/app:1.0.0
          ports:
            - containerPort: 8080
          envFrom:
            - secretRef:
                name: acme-secrets
          readinessProbe:
            httpGet:
              path: /api/v1/health
              port: 8080
```

!!! tip "Template note"
    This is a sketch for documentation examples. Adapt image names, probes, and secrets to your cluster.

## Smoke test after deploy

```bash
curl -fsS https://acme.example.com/api/v1/health
acme projects list --base-url https://acme.example.com
```

## Rollback

Pin the previous image tag and redeploy:

```bash
docker compose up -d --force-recreate
# or
kubectl set image deploy/acme-app acme=acme/app:0.9.0
```
