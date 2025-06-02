# Docker Templates for MCP Development

## 📁 Ready-to-Use Templates

### Basic MCP Server Template

#### Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files first for better caching
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY . .

# Build if using TypeScript
RUN npm run build

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S mcp -u 1001

# Change ownership of the app directory
RUN chown -R mcp:nodejs /app
USER mcp

# Expose port for HTTP transport (if used)
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node health.js || exit 1

# Start the server
CMD ["node", "dist/index.js"]
```

#### docker-compose.yml
```yaml
version: '3.8'

services:
  mcp-server:
    build: .
    container_name: my-mcp-server
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
      - MCP_PORT=3000
      - LOG_LEVEL=info
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "node", "health.js"]
      interval: 30s
      timeout: 10s
      retries: 3
```

#### package.json
```json
{
  "name": "my-mcp-server",
  "version": "1.0.0",
  "description": "My MCP Server",
  "main": "dist/index.js",
  "scripts": {
    "build": "tsc",
    "dev": "tsx src/index.ts",
    "start": "node dist/index.js",
    "docker:build": "docker-compose build",
    "docker:up": "docker-compose up",
    "docker:dev": "docker-compose up --build"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "latest"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "tsx": "^4.0.0",
    "@types/node": "^20.0.0"
  }
}
```

---

## 🚀 Quick Start Commands

```cmd
REM Copy this template to your project
mkdir my-mcp-server
cd my-mcp-server

REM Create the files above, then:
docker-compose up --build

REM For development with auto-reload:
docker-compose -f docker-compose.dev.yml up
```

---

## 🔧 Development-Specific Template

#### docker-compose.dev.yml
```yaml
version: '3.8'

services:
  mcp-server:
    build: 
      context: .
      target: development
    container_name: my-mcp-server-dev
    ports:
      - "3000:3000"
      - "9229:9229"  # Debug port
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
      - DEBUG=mcp:*
    command: npm run dev
```

#### Multi-stage Dockerfile
```dockerfile
# Development stage
FROM node:18-alpine as development
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000 9229
CMD ["npm", "run", "dev"]

# Production stage
FROM node:18-alpine as production
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
RUN addgroup -g 1001 -S nodejs && adduser -S mcp -u 1001
RUN chown -R mcp:nodejs /app
USER mcp
EXPOSE 3000
CMD ["npm", "start"]
```

---

*Use these templates to quickly spin up MCP servers for casual learning and experimentation.*
