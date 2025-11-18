# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Quick Start

```bash
# Install dependencies
npm install

# Build the TypeScript source
npm run build

# Run the CLI (development mode with tsx)
npm start

# Run in development (same as start)
npm run dev
```

**Note**: The CLI connects to the Context7 MCP server (`@upstash/context7-mcp`) to fetch up-to-date library documentation.

## Project Architecture

This is a **modular TypeScript CLI** that provides a REPL interface for querying library documentation through the Model Context Protocol (MCP) via Context7.

### Project Structure

```
src/
├── index.ts (27 lines)                    # Main entry point
├── cli/
│   ├── index.ts                           # Barrel export
│   └── wrapper.ts (210 lines)   # CLI class with REPL logic
├── commands/
│   ├── index.ts                           # Barrel export
│   ├── helpers.ts (37 lines)              # Command info helpers
│   └── runner.ts (59 lines)               # Headless command execution
├── config/
│   ├── index.ts                           # Barrel export
│   └── constants.ts (45 lines)            # Command definitions & server config
├── types/
│   └── index.ts (21 lines)                # TypeScript type definitions
└── utils/
    ├── index.ts                           # Barrel export
    └── argParser.ts (87 lines)            # Command-line argument parser
```

### Core Components

#### Entry Point (`src/index.ts`)

- Bootstraps the application
- Parses command-line arguments
- Routes to interactive or headless mode

#### CLI Module (`src/cli/`)

- **wrapper class**: Main orchestrator managing:
  - `connect()` - Establishes MCP server connection using `@modelcontextprotocol/sdk` client and stdio transport
  - `start()` - Initiates interactive REPL with readline interface
  - `handleCommand()` - Parses and processes user commands
  - `callTool()` - Executes MCP tools with JSON argument parsing
  - `disconnect()` - Graceful cleanup on exit signals (SIGINT/SIGTERM)

#### Commands Module (`src/commands/`)

- `helpers.ts` - Display command information and help
  - `printAvailableCommands()` - Lists all 2 available commands
  - `printCommandDetail(command)` - Shows detailed help for specific command
- `runner.ts` - Execute commands in headless mode
  - `runCommand(command, arg, flag)` - Non-interactive command execution

#### Config Module (`src/config/`)

- `constants.ts` - Centralized configuration
  - `DEFAULT_MCP_SERVER` - MCP server connection settings
  - `COMMANDS[]` - Array of 2 available command names
  - `COMMANDS_INFO[]` - Brief descriptions for each command
  - `COMMANDS_DETAIL[]` - Detailed parameter documentation

#### Utils Module (`src/utils/`)

- `argParser.ts` - Command-line argument handling
  - `parseArguments(args)` - Parses CLI flags and routes execution

#### Types Module (`src/types/`)

- Shared TypeScript interfaces and type definitions

### MCP Client Integration

- Uses `@modelcontextprotocol/sdk` for protocol communication
- Connects to `@upstash/context7-mcp` server via stdio
- Discovers and lists 2 available documentation tools

### REPL Interface

- Custom prompt: `context7>`
- Special commands: `help`, `commands`, `clear`, `exit/quit/q`
- Tool invocation: Direct tool name with JSON arguments

### TypeScript Configuration

- **Target**: ES2022 modules (package.json `"type": "module"`)
- **Output**: Compiles to `dist/` directory with modular structure
- **Declarations**: Generates `.d.ts` files for all modules
- **Source Maps**: Enabled for debugging

## Common Development Tasks

### Building and Running

```bash
# Build TypeScript to JavaScript
npm run build

# Run with tsx (no build step needed)
npm start
# or
npm run dev

# After building, use the compiled binary
npm link  # Link globally
context7-cli  # Use the CLI command
```

### Available Tools

The CLI exposes **2 documentation tools** from the Context7 MCP server:

- **resolve-library-id**: Resolves a package/product name to a Context7-compatible library ID
- **get-library-docs**: Fetches up-to-date documentation for a specific library

### Command Examples

```bash
# Start the CLI in interactive mode
npm start

# Inside the REPL:
context7> commands                          # List all 2 commands
context7> help                              # Show help
context7> resolve-library-id {"name":"mongodb"}  # Resolve library ID
context7> get-library-docs {"context7CompatibleLibraryID":"/mongodb/docs"}  # Get docs
context7> get-library-docs {"context7CompatibleLibraryID":"/vercel/next.js","topic":"routing"}  # Get topic-specific docs
context7> exit                              # Exit

# Headless mode (one-off commands):
npx context7-cli resolve-library-id '{"name":"mongodb"}'
npx context7-cli get-library-docs '{"context7CompatibleLibraryID":"/mongodb/docs"}'
npx context7-cli --commands        # List all commands
npx context7-cli resolve-library-id -h  # Command-specific help
npx context7-cli --help            # General help
npx context7-cli --version         # Show version
```

## Code Structure & Module Responsibilities

### Entry Point (`index.ts`)

- Minimal bootstrapper
- Imports and coordinates other modules
- Handles top-level error catching

### CLI Class (`cli/wrapper.ts`)

- Interactive REPL management
- MCP server connection lifecycle
- User command processing
- Tool execution with result formatting

### Command Helpers (`commands/helpers.ts`)

- Pure functions for displaying command information
- No external dependencies except config
- Easy to test

### Command Runner (`commands/runner.ts`)

- Headless/non-interactive execution
- Single command → result → exit pattern
- Independent MCP client instance

### Constants (`config/constants.ts`)

- Single source of truth for all configuration
- Command definitions (names, descriptions, parameters)
- Server connection settings
- No logic, just data

### Argument Parser (`utils/argParser.ts`)

- CLI flag parsing (--help, --version, --commands, etc.)
- Routing logic for different execution modes
- Command detection and validation

### Key Implementation Details

- **Barrel Exports**: Each module directory has `index.ts` exporting public APIs
- **ES Modules**: All imports use `.js` extensions (TypeScript requirement)
- **Argument Parsing**: Supports JSON arguments for tool parameters
- **Tool Arguments**: Accepts JSON objects (e.g., `{"key": "value"}`)
- **Connection Management**: Uses `@modelcontextprotocol/sdk` with StdioClientTransport
- **Signal Handling**: Graceful shutdown on Ctrl+C (SIGINT) and SIGTERM
- **Error Handling**: Try-catch blocks for connection and tool execution with user-friendly messages

## Dependencies

**Runtime**:

- `@modelcontextprotocol/sdk@^1.0.0` - Official MCP client SDK

**Development**:

- `typescript@^5.0.0` - TypeScript compiler
- `tsx@^4.0.0` - TypeScript execution runtime
- `@types/node@^20.0.0` - Node.js type definitions

## Testing and Verification

### Manual Testing

Key verified features:

- ✅ CLI connection to Context7 MCP server
- ✅ All 2 tools discoverable
- ✅ Interactive REPL functionality
- ✅ Command parsing (JSON)
- ✅ Graceful exit handling
- ✅ Headless mode execution
- ✅ All CLI flags working

### Recommended Test Structure

```
tests/
├── unit/
│   ├── commands/helpers.test.ts
│   ├── commands/runner.test.ts
│   ├── utils/argParser.test.ts
│   └── config/constants.test.ts
├── integration/
│   └── cli/wrapper.test.ts
└── e2e/
    └── fullFlow.test.ts
```

## Important Notes

1. **Modular Structure**: Code is organized into focused modules for better maintainability
2. **ES2022 Modules**: Project uses `"type": "module"` - no CommonJS
3. **MCP Ecosystem**: This CLI is a client that connects to the `@upstash/context7-mcp` server package
4. **Barrel Exports**: Use `from './module/index.js'` for cleaner imports
5. **No Breaking Changes**: Refactoring maintains 100% backward compatibility

## Development Tips

### Adding a New Command

1. Add command name to `COMMANDS` array in `config/constants.ts`
2. Add description to `COMMANDS_INFO` array (same index)
3. Add parameter details to `COMMANDS_DETAIL` array (same index)
4. Done! The CLI will automatically discover it

### Adding a New CLI Flag

1. Edit `utils/argParser.ts`
2. Add flag detection in the `parseArguments()` function
3. Implement the flag's behavior
4. Update help text if needed

### Modifying CLI Behavior

1. Interactive mode logic: `cli/wrapper.ts`
2. Headless mode logic: `commands/runner.ts`
3. Argument routing: `utils/argParser.ts`

### Working with Modules

- Each module is self-contained and independently testable
- Use barrel exports for clean imports: `import { X } from './module/index.js'`
- Follow single responsibility principle - one concern per file
- Keep dependencies flowing in one direction (no circular dependencies)

### Building and Testing

```bash
# Clean build
rm -rf dist && npm run build

# Quick test
npm start

# Test specific functionality
node dist/index.js --help
node dist/index.js --commands
node dist/index.js resolve-library-id -h
```

### Common Patterns

**Importing from modules:**

```typescript
import { wrapper } from './cli/index.js';
import { COMMANDS, DEFAULT_MCP_SERVER } from './config/index.js';
import { parseArguments } from './utils/index.js';
```

**Error handling:**

```typescript
try {
  // Operation
} catch (error: any) {
  console.error('Error:', error.message || error);
  process.exit(1);
}
```

**MCP tool call:**

```typescript
const result = await this.client.callTool(
  {
    name: toolName,
    arguments: arguments_,
  },
  CallToolResultSchema
);
```

## Performance Considerations

- The modular structure has **no performance impact** (tree-shaking applies)
- Same runtime behavior as before
- Same bundle size
- Same startup time

## Future Enhancements

Consider adding:

- Unit tests for each module
- Integration tests with mocked MCP server
- CI/CD pipeline
- Logging module for structured logs
- Validators module for input validation
- Formatters module for output formatting
- Support for API key authentication (--api-key flag)
- Caching for frequently requested documentation
