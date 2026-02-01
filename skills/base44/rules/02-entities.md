# Entities

Entities are your data models in Base44, defined using JSON Schema format.

## Entity File Structure

Entities are stored in `base44/entities/` as `.jsonc` files:

```
base44/entities/
├── User.jsonc
├── Product.jsonc
├── Order.jsonc
└── Category.jsonc
```

## Entity Schema Format

Each entity is a JSON Schema with Base44 extensions:

```jsonc
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Product",
  "description": "A product in the catalog",
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "description": "Product name",
      "minLength": 1,
      "maxLength": 200
    },
    "price": {
      "type": "number",
      "description": "Price in cents",
      "minimum": 0
    },
    "description": {
      "type": "string",
      "description": "Product description"
    },
    "category": {
      "type": "string",
      "description": "Category reference",
      "$ref": "Category"
    },
    "tags": {
      "type": "array",
      "items": {
        "type": "string"
      },
      "description": "Product tags"
    },
    "inStock": {
      "type": "boolean",
      "default": true
    },
    "metadata": {
      "type": "object",
      "additionalProperties": true
    }
  },
  "required": ["name", "price"]
}
```

## Supported Types

| Type | JSON Schema | Notes |
|------|-------------|-------|
| String | `"type": "string"` | Text fields |
| Number | `"type": "number"` | Decimals |
| Integer | `"type": "integer"` | Whole numbers |
| Boolean | `"type": "boolean"` | True/false |
| Array | `"type": "array"` | Lists with `items` |
| Object | `"type": "object"` | Nested objects |
| Reference | `"$ref": "EntityName"` | Relations |

## Entity References

Link entities using `$ref`:

```jsonc
// Order.jsonc
{
  "title": "Order",
  "type": "object",
  "properties": {
    "customer": {
      "type": "string",
      "$ref": "User",
      "description": "Customer who placed the order"
    },
    "items": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "product": {
            "type": "string",
            "$ref": "Product"
          },
          "quantity": {
            "type": "integer",
            "minimum": 1
          }
        }
      }
    }
  }
}
```

## Validation Constraints

```jsonc
{
  "properties": {
    "email": {
      "type": "string",
      "format": "email"
    },
    "age": {
      "type": "integer",
      "minimum": 0,
      "maximum": 150
    },
    "status": {
      "type": "string",
      "enum": ["draft", "published", "archived"]
    },
    "slug": {
      "type": "string",
      "pattern": "^[a-z0-9-]+$"
    }
  }
}
```

## Pushing Entities

Deploy entity schemas to Base44:

```bash
# Push all entities
base44 entities push

# Push with verbose output
base44 entities push --verbose
```

### What Happens on Push

1. Validates all entity schemas
2. Detects changes from remote
3. Creates/updates entity definitions
4. Syncs indexes and constraints

### Push Behavior

- **New entities**: Created in Base44
- **Modified entities**: Schema updated (data preserved)
- **Removed files**: Entities NOT deleted (safety)

To delete an entity, use the Base44 dashboard.

## Best Practices

### Naming Conventions

- Use PascalCase for entity names: `UserProfile.jsonc`
- Use camelCase for property names: `firstName`, `createdAt`
- Be descriptive: `orderStatus` not `status`

### Required Fields

Always define `required` array for essential fields:

```jsonc
{
  "required": ["name", "email", "status"]
}
```

### Descriptions

Add descriptions for documentation and AI context:

```jsonc
{
  "description": "A customer order with line items",
  "properties": {
    "total": {
      "type": "number",
      "description": "Order total in cents, calculated from line items"
    }
  }
}
```

### Default Values

Set sensible defaults:

```jsonc
{
  "properties": {
    "status": {
      "type": "string",
      "enum": ["pending", "active", "completed"],
      "default": "pending"
    },
    "isPublic": {
      "type": "boolean",
      "default": false
    }
  }
}
```

## Example: Complete Entity

```jsonc
// base44/entities/BlogPost.jsonc
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "BlogPost",
  "description": "A blog post with content and metadata",
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "description": "Post title",
      "minLength": 1,
      "maxLength": 200
    },
    "slug": {
      "type": "string",
      "description": "URL-friendly identifier",
      "pattern": "^[a-z0-9-]+$"
    },
    "content": {
      "type": "string",
      "description": "Post content in Markdown"
    },
    "excerpt": {
      "type": "string",
      "description": "Short summary for previews",
      "maxLength": 500
    },
    "author": {
      "type": "string",
      "$ref": "User",
      "description": "Post author"
    },
    "categories": {
      "type": "array",
      "items": {
        "type": "string",
        "$ref": "Category"
      },
      "description": "Post categories"
    },
    "tags": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "status": {
      "type": "string",
      "enum": ["draft", "published", "archived"],
      "default": "draft"
    },
    "publishedAt": {
      "type": "string",
      "format": "date-time",
      "description": "Publication date"
    },
    "featured": {
      "type": "boolean",
      "default": false
    }
  },
  "required": ["title", "slug", "content", "author"]
}
```
