# Cloudflare Workers Package Template

This is a template for creating a Cloudflare Workers package. It is a starting point for creating a package that can be published to NPM.

## Features

- Intended for use with TypeScript
- Comes pre-prepared with the Cloudflare Workers types.
- Compiles to the `esnext` JavaScript target, for use in the Cloudflare Workers runtime.
- Uses Vitest for testing.
- Compiles to ESM only.
- Generates .d.ts files for the package.

## Understanding package.json

This template uses a carefully configured `package.json` to ensure your package works perfectly with Cloudflare Workers and can be published to NPM.

### Example Configuration

```json
{
  "name": "my-package-name",
  "version": "0.1.0",
  "description": "My package description.",
  "author": "Connor Skelland",
  "license": "MIT",
  "engines": {
    "node": ">=18.0.0"
  },
  "keywords": ["something", "something else"],
  "type": "module",
  "types": "dist/index.d.ts",
  "exports": {
    ".": {
      "import": "./dist/index.js",
      "types": "./dist/index.d.ts"
    }
  },
  "scripts": {
    "test": "vitest run",
    "clean": "tsc --build --clean",
    "build": "tsc --build",
    "build:watch": "tsc --watch",
    "prepublishOnly": "npm run clean && npm run build"
  },
  "devDependencies": {
    "@cloudflare/workers-types": "^4.20250816.0",
    "@types/node": "^24.3.0",
    "typescript": "^5.9.2",
    "vitest": "^3.1.0"
  },
  "files": []
}
```

### Field Explanations

| Field                         | Purpose                                                                 | Why It Matters                                                                                                                                |
| ----------------------------- | ----------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **name**                      | Your package's unique identifier on NPM                                 | This is how users will install your package: `npm install my-package-name`                                                                    |
| **version**                   | Current version of your package (follows [semver](https://semver.org/)) | Start at `0.1.0` and increment with each release. NPM uses this to track updates                                                              |
| **description**               | Brief summary of what your package does                                 | Appears in NPM search results and helps users understand your package                                                                         |
| **keywords**                  | Array of search terms                                                   | Helps people discover your package on NPM when searching for related topics                                                                   |
| **type**                      | Set to `"module"` for ES modules                                        | **Critical for Cloudflare Workers** - enables modern `import/export` syntax instead of old `require()`                                        |
| **types**                     | Location of TypeScript type definitions                                 | Points to `dist/index.d.ts` so TypeScript users get autocomplete and type checking                                                            |
| **exports**                   | Defines package entry points                                            | Modern way to specify what users get when they import your package                                                                            |
| **exports["."]**              | The default/main entry point                                            | The `"."` means this is what users get when they do `import pkg from 'my-package-name'`                                                       |
| **exports["."].import**       | Points to your compiled JavaScript                                      | The actual code that runs when someone imports your package                                                                                   |
| **exports["."].types**        | Points to your type definitions                                         | Tells TypeScript where to find type information                                                                                               |
| **scripts**                   | Commands you can run with `npm run`                                     | Automates common tasks like testing and building                                                                                              |
| **scripts.test**              | `vitest run`                                                            | Runs your test suite once                                                                                                                     |
| **scripts.clean**             | `tsc --build --clean`                                                   | Removes all compiled files from the `dist` folder (cleanup before rebuilding)                                                                 |
| **scripts.build**             | `tsc --build`                                                           | Compiles your TypeScript code to JavaScript and generates type definitions using the TypeScript compiler                                      |
| **scripts.build:watch**       | `tsc --watch`                                                           | Continuously watches for changes and rebuilds automatically (useful during development)                                                       |
| **scripts.prepublishOnly**    | `npm run test && npm run clean && npm run build`                        | **Lifecycle hook** - runs tests, cleans and builds your package before publishing to NPM (prevents publishing stale, broken, or unbuilt code) |
| **author**                    | Your name or organization                                               | Shows who maintains the package                                                                                                               |
| **license**                   | Type of license (e.g., MIT, ISC)                                        | Tells users how they can legally use your code. MIT is very permissive                                                                        |
| **engines**                   | Minimum Node.js version required                                        | `>=18.0.0` ensures users have a Node version that supports your code                                                                          |
| **devDependencies**           | Tools needed for development only                                       | These aren't installed when users add your package to their project                                                                           |
| **@cloudflare/workers-types** | TypeScript types for Cloudflare Workers                                 | Provides autocomplete and type checking for Workers-specific APIs                                                                             |
| **@types/node**               | TypeScript types for Node.js                                            | Provides types for Node.js built-in modules                                                                                                   |
| **typescript**                | The TypeScript compiler                                                 | Enables writing type-safe code                                                                                                                |
| **vitest**                    | Fast testing framework                                                  | Modern, fast alternative to Jest with great TypeScript support                                                                                |
| **files**                     | Whitelist of files to publish                                           | Works like a reverse `.gitignore` - only listed files/folders get published. Empty means everything is included                               |

### Key Concepts for Beginners

- **ES Modules vs CommonJS**:
  By setting `"type": "module"`, you're using the modern JavaScript module system. This is required for Cloudflare Workers and allows you to use `import` and `export` instead of the older `require()` syntax.

- **Type Definitions**:
  The `.d.ts` files contain TypeScript type information. Even if users don't use TypeScript, modern editors like VS Code use these files to provide autocomplete suggestions.

- **Lifecycle Scripts**:
  `prepublishOnly` is special - NPM automatically runs it before publishing. This prevents you from accidentally publishing unbuilt code.

- **devDependencies vs dependencies**:
  Development tools go in `devDependencies`. When someone installs your package, they don't get these. Regular `dependencies` (if you add any) are installed with your package.

## Understanding tsconfig.json

The `tsconfig.json` file configures how TypeScript compiles your code. This template is optimized for Cloudflare Workers with strict type checking enabled.

### Example Configuration

```json
{
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./dist",
    "module": "nodenext",
    "target": "esnext",
    "types": ["@cloudflare/workers-types"],
    "declaration": true,
    "noUncheckedIndexedAccess": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noPropertyAccessFromIndexSignature": true,
    "strict": true,
    "verbatimModuleSyntax": true,
    "isolatedModules": true,
    "noUncheckedSideEffectImports": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "test"]
}
```

### Compiler Options Explained

| Option                                 | Purpose                                    | Why It Matters                                                                                                 |
| -------------------------------------- | ------------------------------------------ | -------------------------------------------------------------------------------------------------------------- |
| **rootDir**                            | The root directory of your source code     | Set to `./src` so TypeScript knows where to find your source files                                             |
| **outDir**                             | The output directory for compiled code     | Set to `./dist` so all compiled JavaScript and type definitions go in one place                                |
| **module**                             | The module code generation mode            | `nodenext` ensures compatibility with modern Node.js ESM (ES Modules)                                          |
| **target**                             | The target ECMAScript version              | `esnext` compiles to the latest JavaScript features, perfect for Cloudflare Workers runtime                    |
| **types**                              | Type definitions to include                | `@cloudflare/workers-types` gives you autocomplete and type checking for Cloudflare Workers APIs               |
| **declaration**                        | Generates .d.ts declaration files          | Set to `true` so TypeScript creates type definition files that consumers of your package can use               |
| **noUncheckedIndexedAccess**           | Enforces strict checking of indexed access | Prevents bugs by making TypeScript warn when accessing array/object properties that might not exist            |
| **noUnusedLocals**                     | Enforces that all local variables are used | Helps keep code clean by catching unused variables                                                             |
| **noUnusedParameters**                 | Enforces that all parameters are used      | Catches unused function parameters, improving code quality                                                     |
| **noPropertyAccessFromIndexSignature** | Prevents accessing undefined properties    | Forces you to use bracket notation for dynamic property access, preventing typos                               |
| **strict**                             | Enforces strict type checking              | Enables all strict type checking options - catches more bugs at compile time                                   |
| **verbatimModuleSyntax**               | Forces explicit type imports               | Requires you to write `import type` when only importing types, making bundling more efficient                  |
| **isolatedModules**                    | Enforces strong module boundaries          | Each file must be independently compilable, required for compatibility with bundlers and build tools           |
| **noUncheckedSideEffectImports**       | Prevents importing non-existent modules    | Catches typos in import statements before runtime                                                              |
| **skipLibCheck**                       | Skips checking external library types      | Speeds up compilation by not type-checking node_modules                                                        |
| **forceConsistentCasingInFileNames**   | Enforces consistent casing in file names   | Prevents issues when moving code between case-sensitive (Linux) and case-insensitive (Windows/Mac) filesystems |

### Project Configuration

| Option      | Purpose                           | Why It Matters                                                                               |
| ----------- | --------------------------------- | -------------------------------------------------------------------------------------------- |
| **include** | Files to include in compilation   | `["src/**/*"]` tells TypeScript to compile everything in the `src` folder                    |
| **exclude** | Files to exclude from compilation | Prevents TypeScript from unnecessarily processing `node_modules`, `dist`, and `test` folders |

### Key Concepts for TypeScript Configuration

- **Strict Mode**:
  The `"strict": true` option is your friend. It enables all strict type checking options and helps catch bugs early. While it might seem restrictive at first, it prevents many runtime errors.

- **Module System**:
  Using `"module": "nodenext"` with `"target": "esnext"` ensures your code uses modern ES modules, which is required for Cloudflare Workers. This combination gives you the latest JavaScript features while maintaining proper module resolution.

- **Type Safety**:
  Options like `noUncheckedIndexedAccess` and `noPropertyAccessFromIndexSignature` might seem overly strict, but they prevent common bugs where you access properties that don't exist. Better to catch these at compile time than in production!

- **Build Performance**:
  `skipLibCheck` significantly speeds up compilation by not type-checking your dependencies. Your dependencies should already be tested by their maintainers.
