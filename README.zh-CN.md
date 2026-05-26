# makefile-template-linux

[English](README.md) | [中文](README.zh-CN.md)

一个面向 **Linux/macOS** C/C++ 项目的 Makefile 模板，支持自动源文件发现和依赖追踪。

示例项目将原始二进制文件（`.bin`）转换为 Intel HEX 格式（`.hex`），演示了简洁的扁平化构建体系及自动依赖生成机制。

## 特性

- **自动源文件发现** — 通过 `find` 自动定位 `src/` 下所有 `.c`、`.cpp`、`.s` 文件
- **自动依赖追踪** — 通过 `-MMD -MP` 生成 `.d` 依赖文件，支持正确的增量构建
- **分离构建目录** — 所有 `.o` 和 `.d` 文件输出到 `build/`，保持源码目录整洁
- **多语言支持** — 在同一构建中处理 C、C++ 和汇编源文件
- **VS Code 集成** — 预配置了 `launch.json`（GDB 调试）和 `tasks.json`（构建/清理）

## 环境要求

| 工具 | 版本 | 用途 |
|------|------|------|
| GCC | 7.0+ | C 编译器（`gcc`） |
| GDB | 任意 | 调试器（`gdb`） |
| GNU Make | 3.82+ | 构建自动化 |

通常可通过系统包管理器直接安装：

```bash
# Ubuntu / Debian
sudo apt install build-essential gdb

# macOS（Xcode Command Line Tools）
xcode-select --install
```

## 快速开始

### 1. 克隆模板

```bash
git clone https://github.com/jrtxio/makefile-template-linux.git my-project
cd my-project
```

### 2. 构建

```bash
make
```

### 3. 运行示例

```bash
./build/bin2hex input.bin output.hex
```

读取 `input.bin` 并将 Intel HEX 输出写入 `output.hex`。

### 4. 清理

```bash
make clean
```

## Make 目标

| 目标 | 说明 |
|------|------|
| `all`（默认） | 编译所有源文件并链接为 `build/$(TARGET_EXEC)` |
| `clean` | 删除整个 `build/` 目录 |

## 如何定制

### 修改输出二进制文件名

编辑 `Makefile` 顶部的 `TARGET_EXEC` 变量：

```makefile
TARGET_EXEC := myapp
```

### 添加源码目录

修改 `SRC_DIRS`：

```makefile
SRC_DIRS := ./src ./lib
```

这些目录下的所有 `.c`、`.cpp`、`.s` 文件会被自动发现。

### 添加额外编译选项

通过命令行或在 Makefile 中设置 `CFLAGS` / `CXXFLAGS`：

```bash
make CFLAGS="-O2 -Wall"
```

### 添加链接选项

设置 `LDFLAGS`：

```bash
make LDFLAGS="-lm -lpthread"
```

## 工作原理

1. **源文件发现**：`find $(SRC_DIRS)` 定位所有 `.c`、`.cpp`、`.s` 文件
2. **目标文件映射**：每个源文件映射到 `build/` 下对应的 `.o` 文件（如 `src/main.c` 变为 `build/src/main.c.o`）
3. **依赖生成**：`-MMD -MP` 编译选项在每个 `.o` 文件旁生成 `.d` 文件，确保增量构建正确
4. **链接**：所有目标文件链接为单个可执行文件

## 项目结构

```
.
├── Makefile              # 构建配置 — 目标、选项、规则
├── input.bin             # 示例输入二进制文件
├── src/
│   ├── main.c            # 命令行入口 — 解析参数，调用转换器
│   ├── bin2hex.c         # BIN 转 HEX 转换实现
│   └── bin2hex.h         # 数据结构与 API 声明
├── .vscode/
│   ├── launch.json       # GDB 调试配置
│   └── tasks.json        # 构建与清理任务
└── build/                # （自动生成）构建输出
    ├── *.o               # 编译产生的目标文件
    ├── *.d               # 自动生成的依赖文件
    └── bin2hex           # 最终可执行文件
```

## VS Code 集成

- **构建**：`Ctrl+Shift+B` 执行 `make`
- **清理**：从任务运行器中选择 "Build clean" 任务
- **调试**：按 `F5` 构建并使用 GDB 调试（传入 `input.bin output.hex` 作为参数，在入口处断点）
