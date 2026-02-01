# SDK Integration

Use the `@base44/sdk` package to interact with Base44 from your frontend application.

## Installation

```bash
npm install @base44/sdk
```

## Setup

### Initialize the Client

```typescript
// src/lib/base44.ts
import { Base44 } from '@base44/sdk';

export const base44 = new Base44({
  appId: import.meta.env.VITE_BASE44_APP_ID,
  // Optional: custom API endpoint
  // apiUrl: 'https://api.base44.com'
});
```

### Environment Configuration

```bash
# .env.local
VITE_BASE44_APP_ID=app_abc123xyz
```

## Entity Operations

### Get Entity Client

```typescript
import { base44 } from './lib/base44';

// Get typed entity client
const Products = base44.entities.Product;
const Orders = base44.entities.Order;
```

### Create Records

```typescript
// Create a single record
const product = await base44.entities.Product.create({
  name: 'Widget',
  price: 1999,
  description: 'A useful widget',
  inStock: true
});

console.log(product.id); // Auto-generated ID
```

### Read Records

```typescript
// Get by ID
const product = await base44.entities.Product.get('prod_abc123');

// List with filters
const products = await base44.entities.Product.list({
  filter: {
    inStock: true,
    price: { $lt: 5000 }
  },
  sort: { createdAt: 'desc' },
  limit: 20,
  offset: 0
});

// List all (paginated automatically)
const allProducts = await base44.entities.Product.listAll({
  filter: { inStock: true }
});
```

### Update Records

```typescript
// Update by ID
const updated = await base44.entities.Product.update('prod_abc123', {
  price: 2499,
  inStock: false
});

// Partial update (only specified fields)
await base44.entities.Product.patch('prod_abc123', {
  price: 2499
});
```

### Delete Records

```typescript
// Delete by ID
await base44.entities.Product.delete('prod_abc123');

// Soft delete (if enabled in config)
await base44.entities.Product.softDelete('prod_abc123');
```

## Query Filters

### Comparison Operators

```typescript
const results = await base44.entities.Product.list({
  filter: {
    // Equals
    status: 'active',

    // Not equals
    status: { $ne: 'archived' },

    // Greater than / less than
    price: { $gt: 1000 },
    price: { $gte: 1000 },
    price: { $lt: 5000 },
    price: { $lte: 5000 },

    // In array
    category: { $in: ['electronics', 'gadgets'] },

    // Not in array
    status: { $nin: ['deleted', 'archived'] },

    // Exists
    description: { $exists: true }
  }
});
```

### Logical Operators

```typescript
const results = await base44.entities.Product.list({
  filter: {
    // AND (default)
    inStock: true,
    price: { $lt: 5000 },

    // OR
    $or: [
      { category: 'sale' },
      { featured: true }
    ],

    // AND explicit
    $and: [
      { price: { $gt: 1000 } },
      { price: { $lt: 5000 } }
    ]
  }
});
```

### Text Search

```typescript
const results = await base44.entities.Product.list({
  filter: {
    // Contains (case-insensitive)
    name: { $contains: 'widget' },

    // Starts with
    name: { $startsWith: 'Pro' },

    // Regex
    sku: { $regex: '^SKU-[0-9]+$' }
  }
});
```

## Sorting & Pagination

```typescript
const results = await base44.entities.Product.list({
  // Sort by field
  sort: { createdAt: 'desc' },

  // Multiple sort fields
  sort: [
    { featured: 'desc' },
    { createdAt: 'desc' }
  ],

  // Pagination
  limit: 20,
  offset: 40  // Skip first 40 records
});

// Get total count
const { data, total } = await base44.entities.Product.list({
  filter: { inStock: true },
  includeTotal: true
});
```

## Relations

### Populate Related Records

```typescript
// Get order with customer and products populated
const order = await base44.entities.Order.get('ord_abc123', {
  populate: ['customer', 'items.product']
});

console.log(order.customer.email);
console.log(order.items[0].product.name);
```

### Create with Relations

```typescript
const order = await base44.entities.Order.create({
  customer: 'usr_abc123',  // Reference by ID
  items: [
    { product: 'prod_xyz789', quantity: 2 }
  ],
  status: 'pending'
});
```

## Calling Functions

```typescript
// Call a serverless function
const result = await base44.functions.call('sendEmail', {
  to: 'user@example.com',
  subject: 'Welcome!',
  body: 'Thanks for signing up.'
});

// With typed response
interface EmailResponse {
  success: boolean;
  messageId: string;
}

const result = await base44.functions.call<EmailResponse>('sendEmail', {
  to: 'user@example.com',
  subject: 'Welcome!',
  body: 'Thanks for signing up.'
});
```

## Agent Integration

### Chat with an Agent

```typescript
// Start a conversation
const chat = base44.agents.chat('customer-support');

// Send a message
const response = await chat.send('I need help with my order #12345');

console.log(response.message);  // Agent's response
console.log(response.actions);  // Any actions taken
```

### Conversation History

```typescript
const chat = base44.agents.chat('customer-support');

await chat.send('What is my order status?');
await chat.send('Can you update the shipping address?');

// Get full history
const history = chat.getHistory();

// Clear history
chat.clearHistory();
```

### Streaming Responses

```typescript
const chat = base44.agents.chat('customer-support');

// Stream response
for await (const chunk of chat.stream('Tell me about your return policy')) {
  process.stdout.write(chunk);
}
```

## Authentication

### User Authentication

```typescript
// Login
const user = await base44.auth.login({
  email: 'user@example.com',
  password: 'password123'
});

// Register
const newUser = await base44.auth.register({
  email: 'user@example.com',
  password: 'password123',
  name: 'John Doe'
});

// Get current user
const currentUser = await base44.auth.getCurrentUser();

// Logout
await base44.auth.logout();
```

### Auth State

```typescript
// Check if authenticated
if (base44.auth.isAuthenticated()) {
  // User is logged in
}

// Listen for auth changes
base44.auth.onAuthStateChange((user) => {
  if (user) {
    console.log('Logged in:', user.email);
  } else {
    console.log('Logged out');
  }
});
```

### Protected Requests

The SDK automatically includes auth tokens in requests when logged in.

## React Integration

### Provider Setup

```tsx
// src/App.tsx
import { Base44Provider } from '@base44/sdk/react';
import { base44 } from './lib/base44';

function App() {
  return (
    <Base44Provider client={base44}>
      <YourApp />
    </Base44Provider>
  );
}
```

### Hooks

```tsx
import { useEntity, useAuth, useAgent } from '@base44/sdk/react';

function ProductList() {
  const { data: products, loading, error, refetch } = useEntity('Product', {
    filter: { inStock: true },
    sort: { createdAt: 'desc' }
  });

  if (loading) return <Spinner />;
  if (error) return <Error message={error.message} />;

  return (
    <ul>
      {products.map(p => <li key={p.id}>{p.name}</li>)}
    </ul>
  );
}

function UserProfile() {
  const { user, loading, login, logout } = useAuth();

  if (loading) return <Spinner />;
  if (!user) return <LoginForm onSubmit={login} />;

  return (
    <div>
      <p>Welcome, {user.name}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}

function SupportChat() {
  const { send, messages, loading } = useAgent('customer-support');

  return (
    <div>
      {messages.map((m, i) => (
        <div key={i} className={m.role}>
          {m.content}
        </div>
      ))}
      <input
        onKeyPress={(e) => {
          if (e.key === 'Enter') {
            send(e.target.value);
            e.target.value = '';
          }
        }}
      />
    </div>
  );
}
```

## Error Handling

```typescript
import { Base44Error, NotFoundError, ValidationError } from '@base44/sdk';

try {
  const product = await base44.entities.Product.get('invalid_id');
} catch (error) {
  if (error instanceof NotFoundError) {
    console.log('Product not found');
  } else if (error instanceof ValidationError) {
    console.log('Validation failed:', error.fields);
  } else if (error instanceof Base44Error) {
    console.log('Base44 error:', error.message);
  } else {
    throw error;
  }
}
```

## TypeScript Support

### Generate Types

```bash
# Generate TypeScript types from your entities
base44 types generate

# Output to specific file
base44 types generate --output src/types/base44.ts
```

### Use Generated Types

```typescript
// src/types/base44.ts (generated)
export interface Product {
  id: string;
  name: string;
  price: number;
  description?: string;
  inStock: boolean;
  createdAt: string;
  updatedAt: string;
}

// Usage
import { Product } from './types/base44';

const product: Product = await base44.entities.Product.get('prod_123');
```

## Best Practices

1. **Initialize once** - Create a single base44 instance and export it
2. **Use TypeScript** - Generate and use types for safety
3. **Handle errors** - Always catch and handle Base44 errors
4. **Paginate large lists** - Use `limit` and `offset` for large datasets
5. **Populate sparingly** - Only populate relations when needed
6. **Cache when appropriate** - Use React Query or SWR for caching
