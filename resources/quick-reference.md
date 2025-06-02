# MCP Quick Reference Guide

## 🔍 Essential MCP Concepts

### Core Components
| Component | Purpose | Example |
|-----------|---------|---------|
| **Resources** | Data the AI can access | Files, databases, APIs |
| **Tools** | Functions the AI can call | File operations, calculations |
| **Prompts** | Reusable prompt templates | Code review, analysis |
| **Sampling** | AI model interactions | Generate content, analyze |

### Message Types
- `initialize` - Handshake between client and server
- `list_resources` - Get available resources
- `read_resource` - Access resource content
- `list_tools` - Get available tools
- `call_tool` - Execute a tool function

---

## 🛠️ Development Commands

### Docker Setup
```cmd
REM Create new MCP project
mkdir my-mcp-server
cd my-mcp-server

REM Create Docker files
echo. > Dockerfile
echo. > docker-compose.yml
echo. > package.json

REM Build and run with Docker
docker-compose up --build

REM Run in background
docker-compose up -d

REM View logs
docker-compose logs -f
```

### Development Workflow
```cmd
REM Build MCP server container
docker-compose build

REM Start development server
docker-compose up

REM Rebuild when changes made
docker-compose up --build

REM Stop services
docker-compose down
```

---

## 📝 Code Templates

### Basic Server Structure
```typescript
import { Server } from '@modelcontextprotocol/sdk/server/index.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';

const server = new Server(
  { name: 'my-server', version: '1.0.0' },
  { capabilities: { tools: {} } }
);

// Tool implementation
server.setRequestHandler('tools/call', async (request) => {
  // Handle tool calls
});

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

### Tool Definition Template
```typescript
// In server capabilities
tools: {
  myTool: {
    description: "Description of what this tool does",
    inputSchema: {
      type: "object",
      properties: {
        param1: { type: "string", description: "Parameter description" }
      },
      required: ["param1"]
    }
  }
}
```

---

## 🔧 Troubleshooting Guide

### Common Issues

#### Server Won't Start
- Check Node.js version (requires 18+)
- Verify package.json scripts
- Check TypeScript compilation errors
- Ensure all dependencies are installed

#### Connection Failed
- Verify Claude Desktop configuration
- Check server is running on correct port
- Review server logs for errors
- Confirm transport configuration

#### Tool Not Working
- Validate input schema
- Check tool name matches registration
- Verify error handling
- Test with simple parameters first

### Debug Commands
```cmd
REM Check Docker is running
docker --version

REM View running containers
docker ps

REM Check container logs
docker-compose logs mcp-server

REM Execute commands in container
docker-compose exec mcp-server npm test

REM Rebuild container
docker-compose build --no-cache
```

---

## 📊 Project Structure Template

```
my-mcp-server/
├── src/
│   ├── index.ts          # Main server file
│   ├── tools/            # Tool implementations
│   ├── resources/        # Resource handlers
│   └── types/            # TypeScript types
├── dist/                 # Compiled JavaScript
├── package.json          # Project configuration
├── tsconfig.json         # TypeScript configuration
└── README.md             # Project documentation
```

---

## 🎯 Testing Checklist

### Before Testing
- [ ] Server compiles without errors
- [ ] All dependencies installed
- [ ] Configuration files correct
- [ ] Claude Desktop configured

### During Testing
- [ ] Server starts successfully
- [ ] Claude Desktop connects
- [ ] Tools respond correctly
- [ ] Error handling works
- [ ] Resources are accessible

### After Testing
- [ ] Document any issues found
- [ ] Update progress tracker
- [ ] Plan next development steps
- [ ] Commit working code

---

## 🚀 Performance Tips

### Best Practices
- Use async/await for all operations
- Implement proper error handling
- Cache expensive operations
- Validate inputs thoroughly
- Log important events
- Handle disconnections gracefully

### Optimization
- Minimize resource loading time
- Use streaming for large data
- Implement connection pooling
- Monitor memory usage
- Profile critical code paths

---

*Keep this guide handy as you develop your MCP servers!*
