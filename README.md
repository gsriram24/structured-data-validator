# @gsriram24/structured-data-validator

> **Fork of [@adobe/structured-data-validator](https://github.com/adobe/structured-data-validator)** with additional features.

[![NPM Version](https://img.shields.io/npm/v/%40gsriram24%2Fstructured-data-validator)](https://www.npmjs.com/package/@gsriram24/structured-data-validator)
![Node Current](https://img.shields.io/node/v/%40gsriram24%2Fstructured-data-validator)

A JavaScript library for validating and parsing structured data according to Schema.org specifications and Google Rich Results requirements.

## Additional Features in This Fork

This fork adds the following features on top of Adobe's original library:

### 1. `fieldNames` Property on Validation Errors
Every validation error now includes a `fieldNames` array for precise programmatic access:

```javascript
// Before (Adobe's version) - requires string parsing
{
  issueMessage: 'Required attribute "price" is missing',
  severity: 'ERROR',
  path: [...]
}

// After (this fork) - direct field access
{
  issueMessage: 'Required attribute "price" is missing',
  severity: 'ERROR',
  path: [...],
  fieldNames: ['price']  // ✨ New!
}
```

For `or()` conditions with multiple fields:
```javascript
{
  issueMessage: 'One of the following attributes is required...',
  fieldNames: ['aggregateRating', 'offers', 'review']  // All relevant fields
}
```

Access the primary field with `error.fieldNames[0]}`, or iterate over all fields as needed.

### 2. New Validators for Common Schema Types
Added validators for commonly-used schema.org types:

| Type | Required Fields | 
|------|-----------------|
| `LocalBusiness` | `name`, `address` |
| `Article` | `headline` |
| `Event` | `name`, `startDate`, `location` (or online mode) |
| `FAQPage` | `mainEntity` |
| `HowTo` | `name`, `step` |
| `WebSite` | `name`, `url` |

### 3. Automatic Subtype Inheritance
Subtypes automatically inherit validation from parent types:

| Subtype | Inherits From |
|---------|---------------|
| `Restaurant`, `Store`, `Hotel`, `Dentist` | `LocalBusiness` |
| `NewsArticle`, `BlogPosting`, `TechArticle` | `Article` |
| `MusicEvent`, `SportsEvent`, `Festival` | `Event` |

This enables validation of **100+ schema types** without individual validator files.

---

## Installation

```bash
npm install @gsriram24/structured-data-validator
```

## Usage

```javascript
import { Validator } from '@gsriram24/structured-data-validator';
import WebAutoExtractor from '@marbec/web-auto-extractor';

// Extract structured data from HTML
const extractor = new WebAutoExtractor({ addLocation: true, embedSource: ['rdfa', 'microdata'] });
const extractedData = extractor.parse(sampleHTML);

// Fetch the current schema.org schema
const schemaOrgJson = await (await fetch('https://schema.org/version/latest/schemaorg-all-https.jsonld')).json();

// Create a validator instance
const validator = new Validator(schemaOrgJson);

// Validate the extracted structured data
const results = await validator.validate(extractedData);

// Use fieldNames for precise error handling
results.forEach(issue => {
  if (issue.severity === 'ERROR') {
    console.log(`Field "${issue.fieldNames?.[0]}" has error: ${issue.issueMessage}`);
  }
});
```

### Browser

```js
const { default: WebAutoExtractor } = await import(
  'https://unpkg.com/@marbec/web-auto-extractor@latest/dist/index.js'
);
const { default: Validator } = await import(
  'https://unpkg.com/@gsriram24/structured-data-validator@latest/src/index.js'
);

const extractedData = new WebAutoExtractor({
  addLocation: true,
  embedSource: ['rdfa', 'microdata'],
}).parse(document.documentElement.outerHTML);

const schemaOrgJson = await (
  await fetch('https://schema.org/version/latest/schemaorg-all-https.jsonld')
).json();

const issues = await new Validator(schemaOrgJson).validate(extractedData);
console.log(issues);
```

## Upstream Contributions

The features in this fork have been submitted as PRs to the upstream Adobe repository:
- [PR #57: Add fieldName property](https://github.com/adobe/structured-data-validator/pull/57)
- [PR #58: Add validators for common schema types](https://github.com/adobe/structured-data-validator/pull/58)

Once merged upstream, consider switching back to `@adobe/structured-data-validator`.

## Development

### Prerequisites

- Node.js (>=18.0.0)
- npm

### Setup

```bash
git clone https://github.com/gsriram24/structured-data-validator.git
cd structured-data-validator
npm install
```

### Available Scripts

- `npm test` - Run tests with coverage
- `npm run lint` - Run ESLint
- `npm run format` - Check code formatting
- `npm run format:fix` - Fix code formatting issues

## License

Apache-2.0 (same as upstream)

## Credits

Original library by [Adobe](https://github.com/adobe/structured-data-validator).
