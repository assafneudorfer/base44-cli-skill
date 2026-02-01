# AI Agents

Base44 agents are AI-powered assistants that can interact with your app's data and functions.

## Agent File Structure

Agents are stored in `base44/agents/` as `.jsonc` files:

```
base44/agents/
├── customer-support.jsonc
├── sales-assistant.jsonc
└── data-analyst.jsonc
```

## Agent Configuration Format

```jsonc
{
  "name": "customer-support",
  "displayName": "Customer Support Agent",
  "description": "Helps customers with orders, returns, and product questions",
  "instructions": "You are a helpful customer support agent. Be friendly, professional, and solution-oriented. Always verify customer identity before discussing order details.",
  "model": "gpt-4",
  "tools": {
    "entities": {
      "read": ["Order", "Product", "User"],
      "write": ["Order", "SupportTicket"]
    },
    "functions": ["sendEmail", "createRefund"]
  },
  "settings": {
    "temperature": 0.7,
    "maxTokens": 2000
  }
}
```

## Configuration Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique identifier (kebab-case) |
| `displayName` | string | Human-readable name |
| `description` | string | Brief description of agent purpose |
| `instructions` | string | System prompt / behavior instructions |
| `model` | string | AI model to use |
| `tools` | object | Permissions for entities and functions |
| `settings` | object | Model parameters |

## Tool Permissions

### Entity Access

Control which entities the agent can read/write:

```jsonc
{
  "tools": {
    "entities": {
      // Read-only access
      "read": ["Product", "Category"],

      // Read and write access
      "write": ["Order", "SupportTicket"],

      // Full access to all entities
      "read": ["*"],
      "write": ["*"]
    }
  }
}
```

### Function Access

Specify which functions the agent can call:

```jsonc
{
  "tools": {
    "functions": [
      "sendEmail",
      "createRefund",
      "lookupShipping"
    ]
  }
}
```

## Writing Effective Instructions

```jsonc
{
  "instructions": "You are a sales assistant for an e-commerce store.\n\n## Your Role\n- Help customers find products\n- Answer questions about inventory and pricing\n- Assist with the checkout process\n\n## Guidelines\n- Be friendly and professional\n- Never share internal pricing rules\n- If unsure, offer to connect with human support\n- Always confirm customer intent before making changes\n\n## Capabilities\n- Search products by name, category, or description\n- Check stock availability\n- Calculate shipping estimates\n- Apply discount codes\n\n## Limitations\n- Cannot process payments directly\n- Cannot access customer payment information\n- Cannot modify completed orders"
}
```

## Pushing Agents

Deploy agent configurations to Base44:

```bash
# Push all agents (replaces remote)
base44 agents push

# Push with verbose output
base44 agents push --verbose
```

**Important**: `agents push` replaces ALL remote agents with local configurations.

## Pulling Agents

Download agents from Base44 to local:

```bash
# Pull all agents
base44 agents pull

# Pull with verbose output
base44 agents pull --verbose
```

This overwrites local agent files with remote versions.

## Agent Models

Supported models:

| Model | Use Case |
|-------|----------|
| `gpt-4` | Complex reasoning, nuanced responses |
| `gpt-4-turbo` | Faster GPT-4 with larger context |
| `gpt-3.5-turbo` | Quick responses, simpler tasks |
| `claude-3-opus` | Complex analysis and writing |
| `claude-3-sonnet` | Balanced performance |

## Agent Settings

```jsonc
{
  "settings": {
    // Creativity (0.0 = deterministic, 1.0 = creative)
    "temperature": 0.7,

    // Maximum response length
    "maxTokens": 2000,

    // Response diversity
    "topP": 1.0,

    // Repetition penalty
    "frequencyPenalty": 0.0,
    "presencePenalty": 0.0
  }
}
```

## Example Agents

### Customer Support

```jsonc
// base44/agents/customer-support.jsonc
{
  "name": "customer-support",
  "displayName": "Customer Support",
  "description": "Handles customer inquiries and support tickets",
  "instructions": "You are a friendly customer support agent. Help customers with their orders, answer product questions, and resolve issues. Always maintain a professional and helpful tone. Verify customer identity by asking for their email before discussing order details.",
  "model": "gpt-4",
  "tools": {
    "entities": {
      "read": ["Order", "Product", "User", "SupportTicket"],
      "write": ["SupportTicket"]
    },
    "functions": ["sendEmail", "createRefund"]
  },
  "settings": {
    "temperature": 0.7,
    "maxTokens": 1500
  }
}
```

### Data Analyst

```jsonc
// base44/agents/data-analyst.jsonc
{
  "name": "data-analyst",
  "displayName": "Data Analyst",
  "description": "Analyzes business data and generates insights",
  "instructions": "You are a data analyst. Help users understand their business metrics, generate reports, and identify trends. Present data clearly with specific numbers and percentages. Offer actionable insights based on the data.",
  "model": "gpt-4",
  "tools": {
    "entities": {
      "read": ["Order", "Product", "User", "Analytics"],
      "write": []
    },
    "functions": ["generateReport", "exportData"]
  },
  "settings": {
    "temperature": 0.3,
    "maxTokens": 3000
  }
}
```

### Content Writer

```jsonc
// base44/agents/content-writer.jsonc
{
  "name": "content-writer",
  "displayName": "Content Writer",
  "description": "Creates and edits content for the platform",
  "instructions": "You are a skilled content writer. Create engaging, clear, and SEO-friendly content. Match the brand voice: professional but approachable. Always ask for clarification on tone, audience, and goals before writing.",
  "model": "gpt-4",
  "tools": {
    "entities": {
      "read": ["BlogPost", "Product", "Category"],
      "write": ["BlogPost"]
    },
    "functions": []
  },
  "settings": {
    "temperature": 0.8,
    "maxTokens": 4000
  }
}
```

## Using Agents in Frontend

```typescript
import { base44 } from '@base44/sdk';

// Start a conversation with an agent
const conversation = await base44.agents.chat('customer-support', {
  message: 'I need help with my order #12345'
});

// Continue the conversation
const response = await conversation.send('Can you check the shipping status?');

// Get conversation history
const history = conversation.getHistory();
```

## Best Practices

1. **Principle of least privilege** - Only grant necessary permissions
2. **Clear instructions** - Be specific about behavior and limitations
3. **Test thoroughly** - Verify agent behavior before deployment
4. **Version control** - Track agent changes in git
5. **Monitor usage** - Review agent interactions for improvements
