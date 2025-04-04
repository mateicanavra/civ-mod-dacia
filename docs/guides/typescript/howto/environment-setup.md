# TypeScript Modding Tools: Environment Setup

This guide covers how to set up your development environment for using the Civilization VII TypeScript Modding Tools.

## Prerequisites

Before you begin, ensure you have:
- Node.js installed (version 14 or later)
- Basic knowledge of TypeScript/JavaScript
- Basic understanding of Civilization VII modding concepts

## Installation

### Option 1: From NPM

1. Create a new directory for your mod project
2. Initialize a Node.js project:
   ```bash
   mkdir my-civ7-mod
   cd my-civ7-mod
   npm init -y
   ```
3. Install the TypeScript modding tools:
   ```bash
   npm install civ7-modding-tools typescript ts-node
   ```
4. Create a TypeScript configuration file (`tsconfig.json`):
   ```json
   {
     "compilerOptions": {
       "target": "ES2020",
       "module": "CommonJS",
       "esModuleInterop": true,
       "strict": true,
       "outDir": "./dist"
     },
     "include": ["src/**/*"]
   }
   ```

### Option 2: From GitHub

1. Clone the repository:
   ```bash
   git clone https://github.com/izica/civ7-modding-tools
   cd civ7-modding-tools
   npm install
   ```
2. Copy the examples to use as templates:
   ```bash
   mkdir -p src
   cp examples/* src/
   ```

## Project Structure

Set up your project with this recommended structure:

```
my-civ7-mod/
├── src/           # Your mod code
├── assets/        # Icons and other assets
├── build.ts       # Main build script
├── package.json   # Project configuration
└── tsconfig.json  # TypeScript configuration
```

## Basic Mod Setup

Create a `build.ts` file in your project root:

```typescript
import { Mod } from 'civ7-modding-tools';

// Create a new mod
const mod = new Mod({
    id: 'my-first-mod',      // Unique ID for your mod
    version: '1.0',          // Version string
});

// Add components to the mod (we'll do this in later sections)
// mod.add([...]);

// Build the mod
mod.build('./dist');         // Output directory
```

Run this script with:

```bash
npx ts-node build.ts
```

For more detailed information about the TypeScript modding tools, see:
- [TypeScript Modding Tools Overview](/guides/typescript/typescript-overview.md)
- [TypeScript Modding Tools Technical Implementation](/guides/typescript/typescript-technical.md) 