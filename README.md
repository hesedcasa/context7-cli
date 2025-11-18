# Context7 CLI

[![npm context7-cli package](https://img.shields.io/npm/v/context7-cli.svg)](https://npmjs.org/package/context7-cli)

A command-line interface for connecting to and interacting with Context7 MCP server. Get up-to-date, version-specific code documentation and examples directly from your terminal.

## Features

- 📡 Persistent connection to Context7 MCP server
- 💻 Interactive REPL for documentation queries
- 🚀 Headless mode for one-off command execution
- 📚 Resolve library IDs and fetch current documentation
- 🔧 Command-specific help and documentation
- 🎯 Topic-focused documentation retrieval

## Requirements

- [Node.js](https://nodejs.org/) v18.0 or newer
- [npm](https://www.npmjs.com/)

## Installation

```bash
npm install -g context7-cli
```

## Quick Start

### Interactive Mode

Start the CLI and interact with Context7 through a REPL:

```bash
npx context7-cli
```

Once started, you'll see the `context7>` prompt:

```
Context7 CLI

Usage:

commands         list all the available commands
<command> -h     quick help on <command>
<command> <arg>  run <command> with argument
clear            clear the screen
exit, quit, q    exit the CLI

context7> commands
# Lists all 2 available commands

context7> resolve-library-id {"name":"mongodb"}
# Resolves "mongodb" to a Context7-compatible library ID

context7> get-library-docs {"context7CompatibleLibraryID":"/mongodb/docs","topic":"aggregation"}
# Gets MongoDB documentation focused on aggregation

context7> exit
```

### Headless Mode

Execute single commands without starting the interactive REPL:

```bash
# General format
npx context7-cli <command> '<json_arguments>'

# Examples
npx context7-cli resolve-library-id '{"name":"mongodb"}'
npx context7-cli get-library-docs '{"context7CompatibleLibraryID":"/mongodb/docs"}'
npx context7-cli get-library-docs '{"context7CompatibleLibraryID":"/vercel/next.js","topic":"routing","tokens":5000}'
```

### Command Line Options

```bash
# Show version
npx context7-cli --version
npx context7-cli -v

# List all commands
npx context7-cli --commands

# Get help for specific command
npx context7-cli resolve-library-id -h
npx context7-cli get-library-docs -h

# General help
npx context7-cli --help
npx context7-cli -h
```

## Available Tools

The CLI exposes **2 documentation tools** from the Context7 MCP server:

### resolve-library-id

Resolves a package/product name to a Context7-compatible library ID and returns a list of matching libraries.

**Parameters:**
- `name` (required): string - The package/product name to resolve (e.g., "mongodb", "next.js", "react")

**Example:**
```bash
context7> resolve-library-id {"name":"mongodb"}
```

### get-library-docs

Fetches up-to-date documentation for a specific library, with optional topic focus and token limit.

**Parameters:**
- `context7CompatibleLibraryID` (required): string - Exact Context7-compatible library ID (e.g., "/mongodb/docs", "/vercel/next.js")
- `topic` (optional): string - Focus topic for the docs (e.g., "routing", "hooks", "authentication")
- `tokens` (optional): number - Maximum number of tokens to return (default: 10000)

**Examples:**
```bash
context7> get-library-docs {"context7CompatibleLibraryID":"/mongodb/docs"}
context7> get-library-docs {"context7CompatibleLibraryID":"/vercel/next.js","topic":"routing"}
context7> get-library-docs {"context7CompatibleLibraryID":"/vercel/next.js","topic":"routing","tokens":5000}
```

## Use Cases

### Get Started with a New Library

```bash
# 1. Resolve the library ID
context7> resolve-library-id {"name":"mongodb"}

# 2. Get general documentation
context7> get-library-docs {"context7CompatibleLibraryID":"/mongodb/docs"}

# 3. Get topic-specific documentation
context7> get-library-docs {"context7CompatibleLibraryID":"/mongodb/docs","topic":"aggregation"}
```

### Quick Documentation Lookup

```bash
# Get Next.js routing documentation
npx context7-cli get-library-docs '{"context7CompatibleLibraryID":"/vercel/next.js","topic":"routing"}'

# Get React hooks documentation
npx context7-cli get-library-docs '{"context7CompatibleLibraryID":"/facebook/react","topic":"hooks"}'
```

## About Context7

Context7 is an MCP server that delivers up-to-date, version-specific code documentation and examples directly into LLM prompts and AI code editors. It solves the problem of outdated training data by pulling current documentation straight from the source.

Learn more at [context7.com](https://context7.com) or [github.com/upstash/context7](https://github.com/upstash/context7).

## License

Apache-2.0
