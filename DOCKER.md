# Docker Deployment Guide

This guide explains how to run the LiveKit Voice Agent application using Docker Compose.

## Prerequisites

1. **Docker**: Install Docker Desktop or Docker Engine
   - macOS: Download from [docker.com](https://www.docker.com/products/docker-desktop)
   - Linux: Follow [official Docker installation guide](https://docs.docker.com/engine/install/)
   - Windows: Download Docker Desktop for Windows

2. **Docker Compose**: Included with Docker Desktop, or install separately on Linux

3. **Environment Variables**: Create a `.env.local` file in the project root with:

```bash
# LiveKit Cloud Configuration
LIVEKIT_API_KEY=your_livekit_api_key_here
LIVEKIT_API_SECRET=your_livekit_api_secret_here
LIVEKIT_URL=wss://your-project.livekit.cloud

# Rime TTS API Key
RIME_API_KEY=your_rime_api_key_here

# OpenAI API Key (for LLM)
OPENAI_API_KEY=your_openai_api_key_here

# AssemblyAI API Key (for STT)
ASSEMBLYAI_API_KEY=your_assemblyai_api_key_here
```

Get LiveKit credentials by running:
```bash
lk cloud auth && lk app env -w
```

## Architecture

The Docker Compose setup includes two services:

1. **app**: Next.js frontend application (port 3000)
2. **agent**: LiveKit voice agent with Rime TTS

Both services run on the same `livekit-network` bridge network and share environment variables.

## Quick Start

### 1. Build and Start Services

Build and start both the app and agent:

```bash
docker-compose up --build
```

Or run in detached mode (background):

```bash
docker-compose up -d --build
```

### 2. Access the Application

Once running, access the Next.js app at:
- http://localhost:3000

### 3. View Logs

View logs for all services:
```bash
docker-compose logs -f
```

View logs for a specific service:
```bash
docker-compose logs -f app
docker-compose logs -f agent
```

### 4. Stop Services

Stop all services:
```bash
docker-compose down
```

Stop and remove volumes:
```bash
docker-compose down -v
```

## Development Workflow

### Using Development Mode (Recommended for Development)

For active development with hot reload and code changes reflected immediately:

```bash
# Start in development mode
docker-compose -f docker-compose.dev.yml up

# Or in detached mode
docker-compose -f docker-compose.dev.yml up -d

# View logs
docker-compose -f docker-compose.dev.yml logs -f

# Stop
docker-compose -f docker-compose.dev.yml down
```

In development mode:
- Source code is mounted as volumes
- Changes to code are reflected immediately (hot reload for Next.js)
- The agent runs in dev mode connecting to LiveKit Cloud
- No need to rebuild for code changes

### Rebuilding After Code Changes (Production Mode)

After making code changes, rebuild the affected service:

```bash
# Rebuild both services
docker-compose build

# Rebuild only the app
docker-compose build app

# Rebuild only the agent
docker-compose build agent
```

Then restart:
```bash
docker-compose up -d
```

### Running Individual Services

Start only the app:
```bash
docker-compose up app
```

Start only the agent:
```bash
docker-compose up agent
```

## Troubleshooting

### Agent Won't Start

1. Check environment variables are set:
```bash
docker-compose exec agent printenv | grep -E 'LIVEKIT|RIME|OPENAI|ASSEMBLYAI'
```

2. Check agent logs:
```bash
docker-compose logs agent
```

3. Verify model files were downloaded during build:
```bash
docker-compose exec agent ls -la
```

### App Not Accessible

1. Verify the container is running:
```bash
docker-compose ps
```

2. Check port bindings:
```bash
docker ps | grep render-voice-agent
```

3. Check app logs:
```bash
docker-compose logs app
```

### Connection Issues

If the agent can't connect to LiveKit Cloud:
1. Verify your `.env.local` has the correct LiveKit credentials
2. Check network connectivity from inside the container:
```bash
docker-compose exec agent ping -c 3 8.8.8.8
```

### Rebuilding from Scratch

To completely rebuild from scratch:
```bash
# Stop and remove containers, networks, and volumes
docker-compose down -v

# Remove images
docker rmi render-voice-agent-app render-voice-agent-agent

# Rebuild
docker-compose up --build
```

## Production Deployment

For production deployments, consider:

1. **Use a proper registry**: Push images to Docker Hub, AWS ECR, or similar
```bash
# Tag images
docker tag render-voice-agent-app your-registry/render-voice-agent-app:latest
docker tag render-voice-agent-agent your-registry/render-voice-agent-agent:latest

# Push to registry
docker push your-registry/render-voice-agent-app:latest
docker push your-registry/render-voice-agent-agent:latest
```

2. **Use environment-specific compose files**:
```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

3. **Enable health checks**: Add health check configurations to `docker-compose.yml`

4. **Set resource limits**: Configure memory and CPU limits for each service

5. **Use secrets management**: Instead of `.env.local`, use Docker secrets or a secrets manager

## Advanced Configuration

### Custom Network

To use a custom network instead of the default bridge:

```yaml
networks:
  livekit-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

### Resource Limits

Add resource constraints to services:

```yaml
services:
  agent:
    # ... other config
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 4G
        reservations:
          cpus: '1'
          memory: 2G
```

### Health Checks

Add health checks for better monitoring:

```yaml
services:
  app:
    # ... other config
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000"]
      interval: 30s
      timeout: 10s
      retries: 3
```

## Container Management

### Shell Access

Access a running container's shell:

```bash
# App container
docker-compose exec app sh

# Agent container
docker-compose exec agent sh
```

### Resource Monitoring

Monitor container resource usage:

```bash
docker stats
```

### Clean Up

Remove unused Docker resources:

```bash
# Remove stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove unused volumes
docker volume prune

# Remove everything unused
docker system prune -a
```

## Alternative: Deploying to LiveKit Cloud

Instead of self-hosting with Docker, you can deploy the agent directly to LiveKit Cloud:

```bash
lk agent create
```

This provides:
- Automatic scaling
- Built-in monitoring
- Managed infrastructure
- No Docker configuration needed

See the [AGENT_README.md](agent/AGENT_README.md) for more details on LiveKit Cloud deployment.

## Support

For issues related to:
- Docker setup: Check Docker documentation
- LiveKit agent: See [LiveKit Agents docs](https://docs.livekit.io/agents)
- Rime TTS: See [Rime documentation](https://docs.rime.ai/)

