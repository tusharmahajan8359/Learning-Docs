# Model Context Protocol (MCP) - Learning Guide

> **Your Learning Path**: MCP Fundamentals → Server Implementation → Real Workflow Integration → Validation

---

## 🎯 What Is MCP? (And Why Should You Care?)

Imagine you're chatting with your VS Code Copilot and you ask: _"Show me all open GitHub issues from this week."_

**The question is:** How does Copilot actually access your GitHub data?

**The old way:**

- Every AI tool (Copilot, Claude, ChatGPT) has its own custom GitHub integration
- Duplicate code everywhere
- No standardization
- Lots of maintenance

**The MCP way:**

- One standard protocol for ALL AI tools to access data
- Write a GitHub server once, use it everywhere
- Like USB for AI - plug and play!

That's MCP: **Model Context Protocol** - a universal standard for connecting AI to data sources.

---

## 💡 Why MCP Came Into Picture?

Think about this problem:

```
Before MCP:
├── VS Code Copilot ──→ Custom GitHub Integration (100 lines)
├── Claude Desktop  ──→ Custom GitHub Integration (100 lines)
├── ChatGPT         ──→ Custom GitHub Integration (100 lines)
└── Result: 300 lines of duplicate code!

After MCP:
├── VS Code Copilot ──┐
├── Claude Desktop  ──┼──→ MCP Protocol ──→ ONE GitHub MCP Server
└── ChatGPT         ──┘
    Result: Write once, use everywhere!
```

**The problem MCP solves:**

1. **No Standardization** - Every app had different APIs
2. **Duplicate Work** - Same integrations written multiple times
3. **No Discovery** - Apps couldn't dynamically find what tools are available
4. **Security Chaos** - Different permission systems everywhere

MCP fixed all of this with one universal protocol!

---

## 🏗️ MCP Architecture (The Big Picture)

### The Three Key Players:

```
┌─────────────────────────────────────────────────┐
│  1. YOU (Developer)                             │
│     ↓ Types: "Show me GitHub issues"            │
├─────────────────────────────────────────────────┤
│  2. MCP CLIENT (VS Code Copilot)                │
│     ✓ Receives your prompt                      │
│     ✓ Decides: "I need GitHub data"             │
│     ✓ Calls MCP Server                          │
│     ↓ Uses: MCP Protocol (JSON-RPC)             │
├─────────────────────────────────────────────────┤
│  3. MCP SERVER (GitHub Server)                  │
│     ✓ Exposes tools: list_issues()              │
│     ✓ Fetches data from GitHub API              │
│     ✓ Returns formatted data                    │
│     ↓ Sends back to Client                      │
├─────────────────────────────────────────────────┤
│  4. REAL DATA (GitHub API)                      │
│     Actual issues, PRs, commits, etc.           │
└─────────────────────────────────────────────────┘
```

### In Your VS Code Setup:

| Component      | What It Is                            | Example                  |
| -------------- | ------------------------------------- | ------------------------ |
| **MCP Client** | VS Code Copilot                       | You chat with this       |
| **MCP Server** | `@modelcontextprotocol/server-github` | Provides GitHub tools    |
| **Protocol**   | JSON-RPC over stdio                   | How they talk            |
| **Config**     | `mcp.json`                            | Where you define servers |

---

## 🔄 How MCP Works: The Complete Flow

Let me walk you through what happens when you ask Copilot: _"Show me open GitHub issues"_

### Step 1: You Ask Copilot

```typescript
// You type in VS Code Copilot Chat:
"Show me all open issues created this week";
```

### Step 2: Copilot Analyzes Your Request

```typescript
// Inside VS Code Copilot (MCP Client)
const userPrompt = "Show me all open issues created this week";

// AI analyzes and decides:
const intent = {
  action: "fetch_github_issues",
  needsServer: "github",
  parameters: {
    state: "open",
    since: "2026-04-01",
  },
};
```

### Step 3: Copilot Discovers Available Tools

Copilot asks GitHub MCP Server: _"What can you do?"_

```typescript
// Request from Client → Server
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "id": 1
}

// Response from Server → Client
{
  "jsonrpc": "2.0",
  "result": {
    "tools": [
      {
        "name": "list_issues",
        "description": "List GitHub issues",
        "inputSchema": {
          "type": "object",
          "properties": {
            "repo": { "type": "string" },
            "state": { "type": "string" },
            "since": { "type": "string" }
          }
        }
      }
    ]
  },
  "id": 1
}
```

### Step 4: Copilot Calls the Tool

```typescript
// Client calls the tool
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "list_issues",
    "arguments": {
      "repo": "tushar/my-project",
      "state": "open",
      "since": "2026-04-01"
    }
  },
  "id": 2
}
```

### Step 5: Server Fetches Real Data

```typescript
// Inside MCP GitHub Server
async function handleListIssues(repo: string, state: string, since: string) {
  // 1. Get GitHub token from config
  const token = process.env.GITHUB_TOKEN;

  // 2. Call actual GitHub API
  const response = await fetch(
    `https://api.github.com/repos/${repo}/issues?state=${state}&since=${since}`,
    {
      headers: {
        Authorization: `Bearer ${token}`,
        Accept: "application/vnd.github+json",
      },
    },
  );

  const issues = await response.json();

  // 3. Format and return
  return {
    content: [
      {
        type: "text",
        text: formatIssues(issues),
      },
    ],
  };
}
```

### Step 6: Server Returns Data

```typescript
// Server → Client response
{
  "jsonrpc": "2.0",
  "result": {
    "content": [{
      "type": "text",
      "text": "Found 3 open issues:\n1. Issue #42: Bug in login\n2. Issue #43: Add dark mode\n3. Issue #44: Performance"
    }]
  },
  "id": 2
}
```

### Step 7: Copilot Shows You Results

```
Copilot: I found 3 open issues created this week:

1. **Issue #42: Bug in login flow**
   Created: April 2, 2026

2. **Issue #43: Add dark mode**
   Created: April 3, 2026

3. **Issue #44: Performance optimization**
   Created: April 5, 2026
```

**That's it! The complete MCP flow in 7 steps!** 🎉

---

## 🛠️ Building Your First MCP Server

Now let's create a simple MCP server! This aligns with your _"Practical MCP Server Implementation"_ goal.

### Simple File Reader Server (TypeScript)

```typescript
// server.ts
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";
import fs from "fs/promises";

// 1. Create MCP Server
const server = new Server(
  {
    name: "file-reader-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  },
);

// 2. Define a tool: read_file
server.setRequestHandler("tools/list", async () => {
  return {
    tools: [
      {
        name: "read_file",
        description: "Read contents of a file",
        inputSchema: {
          type: "object",
          properties: {
            path: {
              type: "string",
              description: "File path to read",
            },
          },
          required: ["path"],
        },
      },
    ],
  };
});

// 3. Handle tool calls
server.setRequestHandler("tools/call", async (request) => {
  const { name, arguments: args } = request.params;

  if (name === "read_file") {
    const { path } = args;

    try {
      const content = await fs.readFile(path, "utf-8");

      return {
        content: [
          {
            type: "text",
            text: content,
          },
        ],
      };
    } catch (error) {
      return {
        content: [
          {
            type: "text",
            text: `Error reading file: ${error.message}`,
          },
        ],
        isError: true,
      };
    }
  }

  throw new Error(`Unknown tool: ${name}`);
});

// 4. Start the server
async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("File Reader MCP Server running on stdio");
}

main();
```

### Configure It in VS Code

```json
// ~/.config/Code/User/globalStorage/github.copilot-chat/mcp.json
{
  "mcpServers": {
    "file-reader": {
      "command": "node",
      "args": ["/path/to/your/server.js"]
    }
  }
}
```

### Test It

```typescript
// In Copilot Chat:
"Read the file /home/tushar/project/README.md";

// What happens:
// 1. Copilot calls your file-reader server
// 2. Server reads the file
// 3. Returns content to Copilot
// 4. Copilot shows you the file content!
```

---

## 🌟 Real-World Integration Examples

### Example 1: GitHub + Files (Multi-Server)

```typescript
// You ask Copilot:
"Find all TODO comments in my TypeScript files and create GitHub issues";

// Behind the scenes:
// 1. Copilot calls Filesystem Server → search for "TODO"
// 2. Gets list of TODOs with locations
// 3. For each TODO, calls GitHub Server → create_issue()
// 4. Returns issue URLs to you

// Two servers working together! 🚀
```

### Example 2: Database MCP Server

```typescript
// Create a simple database query server
server.setRequestHandler("tools/list", async () => {
  return {
    tools: [
      {
        name: "query_users",
        description: "Query users from database",
        inputSchema: {
          type: "object",
          properties: {
            limit: { type: "number" },
          },
        },
      },
    ],
  };
});

server.setRequestHandler("tools/call", async (request) => {
  if (request.params.name === "query_users") {
    const users = await db.query("SELECT * FROM users LIMIT ?", [
      request.params.arguments.limit,
    ]);

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(users, null, 2),
        },
      ],
    };
  }
});

// Now Copilot can query your database!
```

---

## ✅ Validation & Testing

### Test Your MCP Server

```typescript
// Use the MCP Inspector tool
npx @modelcontextprotocol/inspector node server.js

// Or test manually with stdio
echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | node server.js
```

### Common Issues & Fixes

| Issue                | Solution                                        |
| -------------------- | ----------------------------------------------- |
| Server not starting  | Check `command` and `args` in config            |
| Tool not found       | Verify `tools/list` returns your tool           |
| Authentication error | Check environment variables (tokens, etc.)      |
| No response          | Ensure you're returning correct JSON-RPC format |

---

## 📋 Key Concepts Summary

### What You Learned:

1. **MCP Fundamentals**
   - MCP = Universal protocol for AI ↔ Data communication
   - Like USB for AI tools
   - Solves duplicate integration problem

2. **Architecture**
   - Client (VS Code Copilot) → Protocol (JSON-RPC) → Server (GitHub, Files, etc.) → Data

3. **Server Implementation**
   - Use `@modelcontextprotocol/sdk`
   - Define tools with `tools/list`
   - Handle calls with `tools/call`
   - Return data in correct format

4. **Real Integration**
   - Configure servers in `mcp.json`
   - Multiple servers can work together
   - Copilot decides which server to call

---

## 🚀 Your Next Steps

1. **✅ Try the file-reader server** - Build it and test it
2. **✅ Create a custom server** - Maybe a server for your specific use case
3. **✅ Explore existing servers** - Check `@modelcontextprotocol/server-*` packages
4. **✅ Integrate into workflow** - Use MCP servers daily in VS Code

---

## 📚 Quick Reference

### MCP Server Template (TypeScript)

```typescript
import { Server } from '@modelcontextprotocol/sdk/server/index.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';

const server = new Server({ name: 'my-server', version: '1.0.0' },
  { capabilities: { tools: {} } });

// List available tools
server.setRequestHandler('tools/list', async () => ({
  tools: [{ name: 'my_tool', description: '...', inputSchema: {...} }]
}));

// Handle tool execution
server.setRequestHandler('tools/call', async (req) => {
  const { name, arguments: args } = req.params;
  // Your logic here
  return { content: [{ type: 'text', text: 'Result' }] };
});

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

### Configuration Template

```json
{
  "mcpServers": {
    "server-name": {
      "command": "node",
      "args": ["/path/to/server.js"],
      "env": {
        "API_KEY": "your-key"
      }
    }
  }
}
```

---

**🎓 Learning Path Complete!** You now understand:

- ✅ What MCP is and why it exists
- ✅ MCP architecture and flow
- ✅ How to build MCP servers
- ✅ How to integrate into real workflows

**Now go build something awesome!** 🚀

---

_Last Updated: April 8, 2026_
