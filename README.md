# makefile-template-linux

[![Language](https://img.shields.io/badge/language-C-blue)]

[English](README.md) | [中文](README.zh-CN.md)

A Makefile template for **Linux/macOS** C and C++ projects with automatic source discovery and dependency tracking.

The example project converts a raw binary file (`.bin`) to Intel HEX format (`.hex`), demonstrating a clean, flat build system with automatic dependency generation.

## Features

- **Automatic source discovery** — Uses `find` to locate all `.c`, `.cpp`, and `.s` files under `src/`
- **Automatic dependency tracking** — Generates `.d` dependency files via `-MMD -MP` for incremental builds
- **Separate build directory** — All `.o` and `.d` files are placed under `build/`, keeping the source tree clean
- **Multi-language support** — Handles C, C++, and assembly sources in the same build
- **VS Code integration** — Pre-configured `launch.json` (GDB debug) and `tasks.json` (build/clean)

## Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| GCC | 7.0+ | C compiler (`gcc`) |
| GDB | Any | Debugging (`gdb`) |
| GNU Make | 3.82+ | Build automation |

These are typically available via the default package manager:

```bash
# Ubuntu / Debian
sudo apt install build-essential gdb

# macOS (Xcode Command Line Tools)
xcode-select --install
```

## Quick Start

### 1. Clone the template

```bash
git clone https://github.com/jrtxio/makefile-template-linux.git my-project
cd my-project
```

### 2. Build

```bash
make
```

### 3. Run the example

```bash
./build/bin2hex input.bin output.hex
```

This reads `input.bin` and writes the Intel HEX output to `output.hex`.

### 4. Clean

```bash
make clean
```

## Make Targets

| Target | Description |
|--------|-------------|
| `all` (default) | Compile all sources and link into `build/$(TARGET_EXEC)` |
| `clean` | Remove the entire `build/` directory |

## How to Customize

### Change the output binary name

Edit the `TARGET_EXEC` variable at the top of the `Makefile`:

```makefile
TARGET_EXEC := myapp
```

### Add source directories

Modify `SRC_DIRS`:

```makefile
SRC_DIRS := ./src ./lib
```

All `.c`, `.cpp`, and `.s` files under these directories are automatically discovered.

### Add extra compiler flags

Set `CFLAGS` or `CXXFLAGS` on the command line or in the Makefile:

```bash
make CFLAGS="-O2 -Wall"
```

### Add linker flags

Set `LDFLAGS`:

```bash
make LDFLAGS="-lm -lpthread"
```

## How It Works

1. **Source discovery**: `find $(SRC_DIRS)` locates all `.c`, `.cpp`, and `.s` files
2. **Object mapping**: Each source file is mapped to a corresponding `.o` file under `build/` (e.g. `src/main.c` becomes `build/src/main.c.o`)
3. **Dependency generation**: The `-MMD -MP` flags create `.d` files alongside each `.o` file, enabling correct incremental rebuilds
4. **Linking**: All object files are linked into a single executable

## Project Structure

```
.
├── Makefile              # Build configuration — targets, flags, rules
├── input.bin             # Example input binary file
├── src/
│   ├── main.c            # CLI entry point — parses arguments, calls converter
│   ├── bin2hex.c         # BIN-to-HEX conversion implementation
│   └── bin2hex.h         # Data structures and API declaration
├── .vscode/
│   ├── launch.json       # GDB debug configuration
│   └── tasks.json        # Build and clean tasks
└── build/                # (generated) Build output
    ├── *.o               # Compiled object files
    ├── *.d               # Auto-generated dependency files
    └── bin2hex           # Final executable
```

## VS Code Integration

- **Build**: `Ctrl+Shift+B` runs `make`
- **Clean**: Select the "Build clean" task from the task runner
- **Debug**: Press `F5` to build and debug with GDB (passes `input.bin output.hex` as arguments, stops at entry)
