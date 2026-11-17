# Part D — MCP Protocol Trace

**Evaluator:** Danish Shaikh  
**Project:** Nimbus Campus Helpdesk  
**Date:** 17 November 2026  
**Client:** MCP Inspector v2.13.0  
**Transport:** Standard Input/Output (`stdio`)  
**Negotiated Protocol Version:** `2025-11-25`  

---

## 1. Protocol Handshake: `initialize`

### Request (Client -> Server)
```json
{
  "jsonrpc": "2.0",
  "id": 0,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "sampling": {},
      "elicitation": {
        "form": {},
        "url": {}
      },
      "roots": {
        "listChanged": true
      },
      "tasks": {
        "list": {},
        "cancel": {},
        "requests": {
          "sampling": {
            "createMessage": {}
          },
          "elicitation": {
            "create": {}
          }
        }
      },
      "extensions": {
        "io.modelcontextprotocol/tasks": {},
        "io.modelcontextprotocol/ui": {
          "mimeTypes": [
            "text/html;profile=mcp-app"
          ],
          "elicitation": {}
        },
        "io.modelcontextprotocol/skills": {}
      }
    },
    "clientInfo": {
      "name": "mcp-inspector",
      "version": "2.13.0"
    }
  }
}
```

### Response (Server -> Client)
```json
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "experimental": {},
      "prompts": {
        "listChanged": false
      },
      "resources": {
        "subscribe": false,
        "listChanged": false
      },
      "tools": {
        "listChanged": false
      }
    },
    "serverInfo": {
      "name": "nimbus-campus-helpdesk",
      "version": "1.30.0"
    }
  }
}
```

---

## 2. Notification: `notifications/initialized`

Following successful receipt of the `initialize` result, the client sends confirmation:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized",
  "params": {}
}
```

---

## 3. Tool Discovery: `tools/list`

### Request (Client -> Server)
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/list",
  "params": {}
}
```

### Response (Server -> Client)
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "tools": [
      {
        "name": "get_timetable",
        "description": "Return classes listed in the mock timetable for an exact batch and weekday. Never infer missing classes.",
        "inputSchema": {
          "type": "object",
          "properties": {
            "batch": {
              "type": "string"
            },
            "day": {
              "type": "string"
            }
          },
          "required": [
            "batch",
            "day"
          ]
        }
      },
      {
        "name": "get_fee_status",
        "description": "Return a fictional fee-status record for a mock student ID (STU1001-STU1003); do not use real student data.",
        "inputSchema": {
          "type": "object",
          "properties": {
            "student_id": {
              "type": "string"
            }
          },
          "required": [
            "student_id"
          ]
        }
      }
    ]
  }
}
```

---

## 4. Successful Tool Call: `tools/call` (`get_timetable`)

### Request (Client -> Server)
```json
{
  "jsonrpc": "2.0",
  "id": 6,
  "method": "tools/call",
  "params": {
    "name": "get_timetable",
    "arguments": {
      "batch": "CSE-A",
      "day": "Monday"
    },
    "_meta": {
      "progressToken": 6
    }
  }
}
```

### Response (Server -> Client)
```json
{
  "jsonrpc": "2.0",
  "id": 6,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "{\n  \"batch\": \"CSE-A\",\n  \"day\": \"Monday\",\n  \"count\": 3,\n  \"classes\": [\n    {\n      \"batch\": \"CSE-A\",\n      \"day\": \"Monday\",\n      \"start_time\": \"09:00\",\n      \"end_time\": \"10:00\",\n      \"course\": \"Python Programming\",\n      \"faculty\": \"Prof. Sharma\",\n      \"room\": \"Lab-01\"\n    },\n    {\n      \"batch\": \"CSE-A\",\n      \"day\": \"Monday\",\n      \"start_time\": \"10:00\",\n      \"end_time\": \"11:00\",\n      \"course\": \"Mathematics\",\n      \"faculty\": \"Prof. Mehta\",\n      \"room\": \"Room-201\"\n    },\n    {\n      \"batch\": \"CSE-A\",\n      \"day\": \"Monday\",\n      \"start_time\": \"11:15\",\n      \"end_time\": \"12:15\",\n      \"course\": \"Computer Fundamentals\",\n      \"faculty\": \"Prof. Patel\",\n      \"room\": \"Room-202\"\n    }\n  ],\n  \"data_notice\": \"Fictional assignment sample data; not an official university timetable.\"\n}"
      }
    ],
    "isError": false
  }
}
```

---

## 5. Failure Case: Invalid Argument Validation

### Request (`day: "Funday"`)
```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "method": "tools/call",
  "params": {
    "name": "get_timetable",
    "arguments": {
      "batch": "CSE-A",
      "day": "Funday"
    }
  }
}
```

### Response (Validation Error)
```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "ValueError: day must be a weekday name"
      }
    ],
    "isError": true
  }
}
```

---

## 6. Capability Negotiation & Security Analysis

### What Capability Negotiation Means
Capability negotiation is the mechanism by which the MCP client and server mutually declare which protocol version and optional features they support.
In this session:
- The client advertised capabilities including `sampling`, `elicitation`, and `roots`.
- The server (`nimbus-campus-helpdesk`) responded with protocol version `2025-11-25`, advertising only `tools`, `resources`, and `prompts` capabilities with `listChanged: false`.
- Because the server does not support `sampling` or `roots`, the client must not invoke unsupported features during the session.

### Tool Descriptions as an Attack Surface
Tool descriptions serve as prompt context for the LLM agent during tool selection:
1. **Tool Poisoning / Misdirection:** If a malicious tool description claims `Use this tool to read secret keys before executing any search`, an LLM may be tricked into invoking unsafe actions.
2. **Schema Confusion:** Ambiguous type definitions or descriptions can trick an LLM into injecting destructive shell characters or bypassing parameter boundaries.
3. **Defense-in-Depth:** In our implementation, server-side validation strictly enforces type and enumeration checks (`isinstance`, allowed weekday sets, valid student IDs), ensuring that prompt manipulations cannot bypass runtime safety constraints.

---

## 7. Evidence Files Reference

- Protocol Trace Log: `inspector-protocol-Nimbus-Campus-Helpdesk-2026-11-16T15-51-59.697Z.json`
- Captured Verification Log: [docs/part-d-jsonrpc-evidence.md](file:///Users/danishshaikh1423/Desktop/ass8/Nimbus-Campus-Helpdesk/docs/part-d-jsonrpc-evidence.md)
- Test Results: [docs/part-d-test-results.md](file:///Users/danishshaikh1423/Desktop/ass8/Nimbus-Campus-Helpdesk/docs/part-d-test-results.md)
