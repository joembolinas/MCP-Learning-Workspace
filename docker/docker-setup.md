# Docker Setup for MCP Development

## 🐳 Docker-Based MCP Development

Since you're using Docker for MCP development, here are the configurations and tips to get you started quickly.

---

## 📋 Quick Docker Setup

### Basic MCP Server Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY . .

# Build TypeScript
RUN npm run build

# Expose port (if using HTTP transport)
EXPOSE 3000

# Start the MCP server
CMD ["npm", "start"]
```

### Docker Compose for Development
```yaml
version: '3.8'
services:
  mcp-server:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
    command: npm run dev
```

---

## 🛠️ Development Workflow

### Quick Commands
```cmd
REM Build and run MCP server
docker-compose up --build

REM Run in background
docker-compose up -d

REM View logs
docker-compose logs -f mcp-server

REM Stop services
docker-compose down

REM Rebuild when you make changes
docker-compose up --build
```

### For Testing Different MCP Servers
```yaml
# docker-compose.yml for multiple servers
version: '3.8'
services:
  hello-mcp:
    build: ./hello-server
    ports:
      - "3001:3000"
    
  file-mcp:
    build: ./file-server
    ports:
      - "3002:3000"
    volumes:
      - ./shared-files:/app/files
    
  api-mcp:
    build: ./api-server
    ports:
      - "3003:3000"
    environment:
      - API_KEY=${API_KEY}
```

---

## 🔧 Development Tips

### Live Reload Setup
Add to your Dockerfile for development:
```dockerfile
# Development stage
FROM node:18-alpine as development
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```

### Volume Mounting for Quick Changes
```yaml
volumes:
  - .:/app           # Mount source code
  - /app/node_modules # Preserve node_modules
```

### Environment Variables
Create `.env` file:
```env
NODE_ENV=development
MCP_SERVER_PORT=3000
API_KEY=your_api_key_here
LOG_LEVEL=debug
```

---

## 🚀 Getting Started

### 1. Create Your First MCP Server
```cmd
REM Create new project directory
mkdir hello-mcp-server
cd hello-mcp-server

REM Create basic files
echo. > Dockerfile
echo. > docker-compose.yml
echo. > package.json
```

### 2. Use the Templates Above
- Copy the Dockerfile template
- Copy the docker-compose.yml template
- Set up your package.json

### 3. Build and Run
```cmd
docker-compose up --build
```

### 4. Test Connection
Your MCP server will be available for Claude Desktop to connect to.

---

## 📝 Docker Best Practices for MCP

### Multi-stage Builds
```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Production stage
FROM node:18-alpine AS production
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
RUN npm run build
CMD ["npm", "start"]
```

### Health Checks
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js || exit 1
```

### Security
```dockerfile
# Don't run as root
RUN addgroup -g 1001 -S nodejs
RUN adduser -S mcp -u 1001
USER mcp
```

---

## 🔗 Useful Docker Commands

```cmd
REM Build specific service
docker-compose build mcp-server

REM Run single command in container
docker-compose exec mcp-server npm test

REM See running containers
docker ps

REM Clean up
docker system prune

REM View container logs
docker logs <container-name>
```

---

*This setup allows you to quickly spin up MCP servers for testing and development without dealing with local Node.js environment issues.*
