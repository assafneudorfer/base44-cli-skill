# Serverless Functions

Base44 functions are TypeScript serverless functions that run on the Base44 platform.

## Function Structure

Functions live in `base44/functions/`:

```
base44/functions/
├── sendEmail.ts
├── processPayment.ts
├── generateReport.ts
└── webhooks/
    ├── stripe.ts
    └── github.ts
```

## Basic Function Format

```typescript
// base44/functions/hello.ts
import { FunctionContext } from '@base44/sdk/functions';

export default async function handler(
  context: FunctionContext
): Promise<{ message: string }> {
  const { params, user } = context;

  return {
    message: `Hello, ${params.name || 'World'}!`
  };
}
```

## Function Context

The `context` object provides:

```typescript
interface FunctionContext {
  // Request parameters (query + body merged)
  params: Record<string, any>;

  // Authenticated user (if any)
  user: {
    id: string;
    email: string;
    // ... other user fields
  } | null;

  // HTTP request details
  request: {
    method: string;
    headers: Record<string, string>;
    body: any;
  };

  // Entity access
  entities: EntityClient;

  // Environment variables
  env: Record<string, string>;
}
```

## Working with Entities

Access entities directly in functions:

```typescript
import { FunctionContext } from '@base44/sdk/functions';

export default async function handler(context: FunctionContext) {
  const { entities, params } = context;

  // Create
  const product = await entities.Product.create({
    name: params.name,
    price: params.price
  });

  // Read
  const products = await entities.Product.list({
    filter: { inStock: true },
    limit: 10
  });

  // Update
  await entities.Product.update(params.id, {
    price: params.newPrice
  });

  // Delete
  await entities.Product.delete(params.id);

  return { success: true, product };
}
```

## Authentication & Authorization

```typescript
import { FunctionContext } from '@base44/sdk/functions';

export default async function handler(context: FunctionContext) {
  const { user, params } = context;

  // Require authentication
  if (!user) {
    throw new Error('Authentication required');
  }

  // Check permissions
  if (user.role !== 'admin') {
    throw new Error('Admin access required');
  }

  // Proceed with authorized action
  return { userId: user.id };
}
```

## Error Handling

```typescript
import { FunctionContext } from '@base44/sdk/functions';

export default async function handler(context: FunctionContext) {
  const { params } = context;

  // Validation
  if (!params.email) {
    throw new Error('Email is required');
  }

  try {
    // Operation that might fail
    const result = await someOperation();
    return { success: true, result };
  } catch (error) {
    // Log for debugging
    console.error('Operation failed:', error);

    // Return user-friendly error
    throw new Error('Failed to process request');
  }
}
```

## Environment Variables

Access env vars through context:

```typescript
export default async function handler(context: FunctionContext) {
  const { env } = context;

  const apiKey = env.STRIPE_SECRET_KEY;
  const webhookUrl = env.WEBHOOK_URL;

  // Use environment variables
}
```

Set environment variables in the Base44 dashboard or config.

## Deploying Functions

```bash
# Deploy all functions
base44 functions deploy

# Deploy with verbose output
base44 functions deploy --verbose
```

### Deployment Process

1. TypeScript compilation
2. Dependency bundling
3. Upload to Base44
4. Function endpoints activated

### Function Endpoints

After deployment, functions are available at:

```
https://api.base44.com/v1/apps/{app-id}/functions/{function-name}
```

## Calling Functions from Frontend

Using the SDK:

```typescript
import { base44 } from '@base44/sdk';

// Call a function
const result = await base44.functions.call('sendEmail', {
  to: 'user@example.com',
  subject: 'Hello',
  body: 'Welcome!'
});
```

## Example Functions

### Send Email

```typescript
// base44/functions/sendEmail.ts
import { FunctionContext } from '@base44/sdk/functions';

interface EmailParams {
  to: string;
  subject: string;
  body: string;
}

export default async function handler(context: FunctionContext) {
  const { params, env } = context;
  const { to, subject, body } = params as EmailParams;

  // Validate
  if (!to || !subject || !body) {
    throw new Error('Missing required fields: to, subject, body');
  }

  // Send via email service
  const response = await fetch('https://api.sendgrid.com/v3/mail/send', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${env.SENDGRID_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      personalizations: [{ to: [{ email: to }] }],
      from: { email: env.FROM_EMAIL },
      subject,
      content: [{ type: 'text/plain', value: body }]
    })
  });

  if (!response.ok) {
    throw new Error('Failed to send email');
  }

  return { success: true, message: 'Email sent' };
}
```

### Process Webhook

```typescript
// base44/functions/webhooks/stripe.ts
import { FunctionContext } from '@base44/sdk/functions';

export default async function handler(context: FunctionContext) {
  const { request, entities, env } = context;

  // Verify webhook signature
  const signature = request.headers['stripe-signature'];
  // ... verification logic

  const event = request.body;

  switch (event.type) {
    case 'payment_intent.succeeded':
      await entities.Order.update(event.data.object.metadata.orderId, {
        status: 'paid',
        paidAt: new Date().toISOString()
      });
      break;

    case 'payment_intent.failed':
      await entities.Order.update(event.data.object.metadata.orderId, {
        status: 'payment_failed'
      });
      break;
  }

  return { received: true };
}
```

### Generate Report

```typescript
// base44/functions/generateReport.ts
import { FunctionContext } from '@base44/sdk/functions';

export default async function handler(context: FunctionContext) {
  const { entities, params, user } = context;

  if (!user) {
    throw new Error('Authentication required');
  }

  const { startDate, endDate } = params;

  // Fetch data
  const orders = await entities.Order.list({
    filter: {
      createdAt: { $gte: startDate, $lte: endDate },
      status: 'completed'
    }
  });

  // Calculate metrics
  const totalRevenue = orders.reduce((sum, o) => sum + o.total, 0);
  const orderCount = orders.length;
  const avgOrderValue = orderCount > 0 ? totalRevenue / orderCount : 0;

  return {
    period: { startDate, endDate },
    metrics: {
      totalRevenue,
      orderCount,
      avgOrderValue
    }
  };
}
```

## Best Practices

1. **Validate input** - Always validate params before processing
2. **Handle errors gracefully** - Return meaningful error messages
3. **Use TypeScript** - Leverage types for safety
4. **Keep functions focused** - One function, one purpose
5. **Log appropriately** - Use console.log for debugging
6. **Secure sensitive operations** - Check authentication/authorization
