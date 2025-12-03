---
status: approved
date: 2024-02-15
applies-to: libraries
authors:
  - "@frontend-platform"
reviewers:
  - "@architecture-review"
gitignore:
  - "node_modules/"
  - "dist/"
  - "coverage/"
  - ".turbo/"
  - "*.tsbuildinfo"
scaffold:
  directories:
    - "src/"
    - "tests/"
  files:
    - path: "tsconfig.json"
      content: |
        {
          "compilerOptions": {
            "strict": true,
            "noUncheckedIndexedAccess": true,
            "noImplicitOverride": true,
            "exactOptionalPropertyTypes": true,
            "declaration": true,
            "declarationMap": true,
            "sourceMap": true,
            "target": "ES2020",
            "module": "NodeNext",
            "moduleResolution": "NodeNext",
            "outDir": "dist",
            "rootDir": "src"
          },
          "include": ["src/**/*"],
          "exclude": ["node_modules", "dist"]
        }
    - path: "vitest.config.ts"
      content: |
        import { defineConfig } from 'vitest/config';

        export default defineConfig({
          test: {
            globals: true,
            coverage: {
              provider: 'v8',
              reporter: ['text', 'json', 'html'],
              thresholds: {
                statements: 80,
                branches: 80,
                functions: 80,
                lines: 80,
              },
            },
          },
        });
    - path: ".prettierrc"
      content: |
        {
          "semi": true,
          "singleQuote": true,
          "trailingComma": "es5",
          "printWidth": 100
        }
    - path: "src/index.ts"
      content: |
        // Export your public API here
        export {};
---

# STD-0002: TypeScript Libraries

## Overview

This standard defines requirements for building reusable TypeScript libraries. It covers language configuration, build setup, testing, documentation, and publishing.

Libraries following this standard can be consumed by both internal and external projects.

## Scope

### Applies To

* Shared utility libraries
* UI component libraries
* API client libraries
* Internal packages in monorepos

### Does Not Apply To

* Applications (see STD-0004)
* Backend services (see STD-0001)
* One-off scripts

## Standard

### Language & Runtime

#### MUST

* Use TypeScript 5.0 or later
* Target ES2020 or later for output
* Enable `strict` mode in tsconfig.json
* Export both ESM and CommonJS formats

#### SHOULD

* Use Node.js 18 LTS or later for development
* Keep TypeScript version updated within 6 months of release

#### MAY

* Use `tsx` for development scripts
* Use path aliases with proper resolution config

### Repository Structure

#### MUST

* Follow this structure:
  ```
  ├── src/
  │   ├── index.ts          # Public exports
  │   └── {modules}/
  ├── tests/
  ├── dist/                  # Generated (gitignored)
  ├── package.json
  ├── tsconfig.json
  ├── tsconfig.build.json
  ├── vitest.config.ts
  └── README.md
  ```
* Export all public API from `src/index.ts`
* Keep internal modules private (not exported)

#### SHOULD

* Use `src/` for source code, `tests/` for tests
* Co-locate unit tests with source (`.test.ts` suffix)
* Place integration tests in `tests/`

### Package Configuration

#### MUST

* Configure package.json exports correctly:
  ```json
  {
    "name": "@org/package-name",
    "type": "module",
    "exports": {
      ".": {
        "types": "./dist/index.d.ts",
        "import": "./dist/index.js",
        "require": "./dist/index.cjs"
      }
    },
    "files": ["dist"],
    "sideEffects": false
  }
  ```
* Use scoped package names (`@org/package-name`)
* Specify `engines.node` requirement
* Include `types` field pointing to declarations

#### SHOULD

* Use `peerDependencies` for framework dependencies
* Keep `dependencies` minimal
* Use `devDependencies` for build tools

### TypeScript Configuration

#### MUST

* Use strict configuration:
  ```json
  {
    "compilerOptions": {
      "strict": true,
      "noUncheckedIndexedAccess": true,
      "noImplicitOverride": true,
      "exactOptionalPropertyTypes": true,
      "declaration": true,
      "declarationMap": true,
      "sourceMap": true
    }
  }
  ```
* Generate declaration files (`.d.ts`)
* Generate source maps for debugging

#### SHOULD

* Use `verbatimModuleSyntax` for explicit import types
* Enable `isolatedModules` for faster builds

### Build & Bundle

#### MUST

* Use `tsup` or `unbuild` for building
* Output both ESM (`.js`) and CJS (`.cjs`) formats
* Generate TypeScript declarations
* Minify production builds

#### SHOULD

* Use esbuild for fast builds
* Tree-shake unused code
* Bundle dependencies only when necessary

### Testing

#### MUST

* Use Vitest for testing
* Achieve minimum 80% code coverage
* Test public API thoroughly
* Test edge cases and error conditions

#### SHOULD

* Use `@testing-library/*` for component testing
* Mock external dependencies
* Test type definitions with `tsd` or `expect-type`

#### MAY

* Use snapshot testing for complex outputs
* Use property-based testing for parsing logic

### Documentation

#### MUST

* Include README.md with:
  - Installation instructions
  - Basic usage examples
  - API overview
  - Link to full documentation
* Use TSDoc comments on all public exports
* Document breaking changes in CHANGELOG.md

#### SHOULD

* Generate API documentation with TypeDoc
* Include runnable examples
* Document common patterns and gotchas

### Versioning & Publishing

#### MUST

* Follow semantic versioning (semver)
* Document breaking changes in CHANGELOG.md
* Tag releases in git
* Publish to internal registry

#### SHOULD

* Use conventional commits
* Automate releases with changesets or semantic-release
* Include migration guides for major versions

## Examples

### Compliant Example

```typescript
// src/index.ts - Clean public API
export { createClient } from './client.js';
export type { Client, ClientOptions } from './client.js';
export { ValidationError, NetworkError } from './errors.js';
export type { ApiResponse } from './types.js';
```

```typescript
// src/client.ts - Well-documented module
/**
 * Configuration options for the API client.
 */
export interface ClientOptions {
  /** Base URL for API requests */
  baseUrl: string;
  /** Request timeout in milliseconds (default: 30000) */
  timeout?: number;
  /** Custom headers to include in all requests */
  headers?: Record<string, string>;
}

/**
 * Creates a new API client instance.
 * 
 * @param options - Client configuration
 * @returns Configured client instance
 * 
 * @example
 * ```ts
 * const client = createClient({
 *   baseUrl: 'https://api.example.com',
 *   timeout: 5000,
 * });
 * 
 * const user = await client.get('/users/1');
 * ```
 */
export function createClient(options: ClientOptions): Client {
  // Implementation
}
```

### Non-Compliant Example

```typescript
// Bad: No types, loose configuration, poor exports
module.exports = {
  client: require('./client'),  // VIOLATION: CommonJS only
  utils: require('./utils'),    // VIOLATION: Exposing internals
}

// tsconfig.json
{
  "compilerOptions": {
    "strict": false,           // VIOLATION: strict must be true
    "any": true                // VIOLATION: implicit any
  }
}
```

**Why this violates the standard:**
- CommonJS-only export (MUST support ESM)
- Exposing internal modules (MUST export from index.ts)
- Strict mode disabled (MUST enable strict)
- Missing type declarations

## Enforcement

* **Automated checks**:
  - `tsc --noEmit` for type checking
  - `vitest run --coverage` for test coverage
  - `publint` for package.json validation
  - `attw` (Are The Types Wrong) for export validation

* **Manual review**:
  - API design review for new public exports
  - Documentation completeness check

* **Exceptions process**:
  - Document in PR why exception is needed
  - Requires @frontend-platform approval

## Tooling Integration

### Linter Configuration

```json
// .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/strict-type-checked"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "./tsconfig.json"
  },
  "rules": {
    "@typescript-eslint/explicit-function-return-type": "error",
    "@typescript-eslint/no-explicit-any": "error"
  }
}
```

### CI/CD Integration

```yaml
# .github/workflows/ci.yml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
          
      - run: pnpm install
      - run: pnpm type-check
      - run: pnpm test --coverage
      - run: pnpm build
      - run: pnpm publint
      - run: npx attw --pack
```

### IDE Setup

* **VS Code**: Use `typescript.tsdk` to pin workspace TypeScript version
* **Extensions**: ESLint, Prettier, Error Lens

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-02-15 | @frontend-platform | Initial version |

## Rationale

* **Strict TypeScript**: Catches bugs at compile time, improves refactoring confidence
* **Dual ESM/CJS**: Maximum compatibility with consuming projects
* **80% coverage**: Libraries need higher coverage as they're widely reused
* **TSDoc**: Enables IDE intellisense for consumers

## References

* [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/)
* [publint](https://publint.dev/) - Package validation
* [Are The Types Wrong?](https://arethetypeswrong.github.io/) - Export validation
* [tsup](https://tsup.egoist.dev/) - Bundler documentation
