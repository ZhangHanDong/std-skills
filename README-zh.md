# Rust 标准库技能

[English](README.md) | [日本語](README-ja.md)

> Rust 标准库技能集合

## 目录结构

```
rust-std-skills/
├── SKILL.md              # 主入口（概览 + 快速参考）
├── skills/               # 子模块技能
│   ├── core-types/      # Option, Result, String, Box, Rc, Cell
│   ├── collections/     # Vec, HashMap, HashSet, BTreeMap 等
│   ├── sync/            # Arc, Mutex, RwLock, Atomic*, thread, channels
│   ├── traits/          # Iterator, From/Into, Clone, Debug 等
│   ├── io/              # Read, Write, BufReader, File, stdin/stdout
│   ├── system/          # fs, path, env, process, net, time
│   └── utilities/       # mem, fmt, error, marker traits
└── references/          # 详细文档
    ├── _shared/         # 共享规则 (rust-defaults.md)
    ├── core-types/      # Option, Result, String, Box, Rc, Cell
    ├── collections/     # Vec, HashMap 参考
    ├── sync/            # Mutex, Arc, thread, mpsc, 原子类型
    ├── traits/          # 可派生 trait, 转换 trait
    ├── io/              # I/O trait, 文件操作
    ├── system/          # fs, path, env, process, net, time
    └── utilities/       # mem, fmt, error, marker
```

## 使用方法

### 作为 Claude Code 技能使用

将此目录复制或符号链接到 Claude Code 技能目录：

```bash
# 方式 1: 符号链接
ln -s /path/to/rust-std-skills ~/.claude/skills/rust-std

# 方式 2: 复制
cp -r /path/to/rust-std-skills ~/.claude/skills/rust-std
```

### 触发关键词

技能在以下关键词时激活：
- `std::`, `标准库`, `rust std`
- `Option`, `Result`, `String`, `Box`, `Rc`, `Cell`, `RefCell`
- `Vec`, `HashMap`, `HashSet`, `BTreeMap`
- `Arc`, `Mutex`, `RwLock`, `Atomic*`, `mpsc`, `thread`
- `Iterator`, `From`, `Into`, `Clone`, `Debug`, `Display`
- `Read`, `Write`, `File`, `BufReader`, `Path`, `PathBuf`
- `fs`, `env`, `process`, `Command`, `TcpListener`, `Duration`
- `Send`, `Sync`, `Copy`, `Drop`, `Sized`, `PhantomData`
- 中文关键词：`向量`, `哈希表`, `迭代器`, `特征`, `智能指针`, `内存操作` 等

## 模块说明

| 模块 | 用途 | 核心类型 |
|------|------|----------|
| **core-types** | 基础类型 | `Option`, `Result`, `String`, `Box`, `Rc`, `Cell` |
| **collections** | 数据结构 | `Vec`, `HashMap`, `HashSet`, `BTreeMap`, `VecDeque` |
| **sync** | 并发原语 | `Arc`, `Mutex`, `RwLock`, `Atomic*`, `thread`, `mpsc` |
| **traits** | 核心 trait | `Iterator`, `From/Into`, `Clone`, `Debug`, `Default` |
| **io** | 输入输出 | `Read`, `Write`, `BufReader`, `File`, `stdin/stdout` |
| **system** | 系统交互 | `fs`, `path`, `env`, `process`, `net`, `time` |
| **utilities** | 工具模块 | `mem`, `fmt`, `error`, `marker` |

## 快速参考

### 核心类型
- `Option<T>` - 可选值 (Some/None)
- `Result<T, E>` - 成功/错误结果
- `String` / `&str` - UTF-8 字符串
- `Box<T>` - 堆分配
- `Rc<T>` / `Arc<T>` - 引用计数
- `Cell<T>` / `RefCell<T>` - 内部可变性

### 集合类型
- `Vec<T>` - 可增长数组
- `HashMap<K, V>` - 哈希表
- `HashSet<T>` - 唯一值集合
- `BTreeMap<K, V>` - 有序映射

### 并发
- `Arc<Mutex<T>>` - 共享可变状态
- `thread::spawn` - 创建线程
- `mpsc::channel` - 消息传递
- `Atomic*` - 无锁原语

### 系统
- `std::fs` - 文件操作
- `std::path` - 路径处理
- `std::env` - 环境变量
- `std::process` - 进程管理
- `std::net` - TCP/UDP 网络
- `std::time` - 时间处理

### 核心 Trait
- `Clone` / `Copy` - 复制
- `Debug` / `Display` - 格式化
- `From` / `Into` - 类型转换
- `Iterator` - 迭代协议
- `Send` / `Sync` - 线程安全
- `Error` - 错误处理

## 版本信息

- **Rust**: 1.92.0
- **Edition**: 2024
- **最后更新**: 2026-01-19
