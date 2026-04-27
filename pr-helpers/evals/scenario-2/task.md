# Fix Review Feedback in a TypeScript API Project

## Problem/Feature Description

You are working on a TypeScript API project that has a code generation pipeline: the source schemas in `src/schemas/` drive generated TypeScript types in `generated/types.ts` and a generated OpenAPI spec in `generated/openapi.json`. A reviewer has flagged two issues during code review. Your job is to fix the problems, regenerate the derived files, ensure the code passes linting, and prepare a commit.

The project uses a simple build pipeline: running `node scripts/generate.js` reads the source schemas and produces the generated files. The linter is run via `node scripts/lint.js`.

## Output Specification

Produce the following:

1. The fixed source files in `src/` — apply minimal, focused changes that address only what was flagged.
2. Regenerated files in `generated/` — produced by running the generation script after fixing the source.
3. **`commit_message.txt`** — The commit message you would use for this change.
4. **`staged_files.txt`** — A list of files (one per line) that should be staged for the commit.
5. **`workflow_log.md`** — A brief log of the steps you took, in order: what you fixed, what you regenerated, what verification you ran.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/src/schemas/product.ts ===============
export interface ProductSchema {
  id: number;
  name: string;
  price: number;
  description: string;
  category_id: number;
}

// Review comment #1: "The id field should be a string UUID, not a number.
// We migrated to UUIDs last sprint."
// Review comment #2: "price should be represented as an integer (cents)
// with a separate display formatter, not a float."

=============== FILE: inputs/src/handlers/product.ts ===============
import { ProductSchema } from '../schemas/product';

export function formatProduct(product: ProductSchema): string {
  return JSON.stringify({
    id: product.id,
    name: product.name,
    price: `$${product.price.toFixed(2)}`,
    description: product.description
  });
}

export function validateProduct(data: unknown): data is ProductSchema {
  const p = data as ProductSchema;
  return typeof p.id === 'number' && typeof p.name === 'string' && typeof p.price === 'number';
}

=============== FILE: inputs/generated/types.ts ===============
// AUTO-GENERATED — DO NOT EDIT MANUALLY
// Generated from src/schemas/

export type Product = {
  id: number;
  name: string;
  price: number;
  description: string;
  category_id: number;
};

=============== FILE: inputs/generated/openapi.json ===============
{
  "components": {
    "schemas": {
      "Product": {
        "type": "object",
        "properties": {
          "id": { "type": "integer" },
          "name": { "type": "string" },
          "price": { "type": "number" },
          "description": { "type": "string" },
          "category_id": { "type": "integer" }
        }
      }
    }
  }
}

=============== FILE: inputs/scripts/generate.js ===============
const fs = require('fs');
const path = require('path');

// Read the source schema
const schemaPath = path.join(__dirname, '..', 'src', 'schemas', 'product.ts');
const schemaContent = fs.readFileSync(schemaPath, 'utf-8');

// Simple parser: extract interface fields
const fieldRegex = /(\w+):\s*(string|number|boolean)/g;
const fields = {};
let match;
while ((match = fieldRegex.exec(schemaContent)) !== null) {
  fields[match[1]] = match[2];
}

// Generate types.ts
const typeFields = Object.entries(fields)
  .map(([name, type]) => `  ${name}: ${type};`)
  .join('\n');

const typesContent = `// AUTO-GENERATED — DO NOT EDIT MANUALLY
// Generated from src/schemas/

export type Product = {
${typeFields}
};
`;

fs.writeFileSync(
  path.join(__dirname, '..', 'generated', 'types.ts'),
  typesContent
);

// Generate openapi.json
const typeMap = { string: 'string', number: 'number', boolean: 'boolean' };
const properties = {};
for (const [name, type] of Object.entries(fields)) {
  properties[name] = { type: typeMap[type] || 'string' };
}

const openapi = {
  components: {
    schemas: {
      Product: {
        type: 'object',
        properties
      }
    }
  }
};

fs.writeFileSync(
  path.join(__dirname, '..', 'generated', 'openapi.json'),
  JSON.stringify(openapi, null, 2)
);

console.log('Generated types.ts and openapi.json');

=============== FILE: inputs/scripts/lint.js ===============
const fs = require('fs');
const path = require('path');

// Simple linter: check that generated files match source schemas
const schemaPath = path.join(__dirname, '..', 'src', 'schemas', 'product.ts');
const typesPath = path.join(__dirname, '..', 'generated', 'types.ts');

const schema = fs.readFileSync(schemaPath, 'utf-8');
const types = fs.readFileSync(typesPath, 'utf-8');

// Check id field type consistency
if (schema.includes('id: string') && types.includes('id: number')) {
  console.error('LINT ERROR: id type mismatch between schema and generated types');
  process.exit(1);
}

if (schema.includes('id: number') && types.includes('id: string')) {
  console.error('LINT ERROR: id type mismatch between schema and generated types');
  process.exit(1);
}

console.log('Lint passed');
