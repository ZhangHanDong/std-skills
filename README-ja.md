# Rust 標準ライブラリスキル

[English](README.md) | [中文](README-zh.md)

> Rust 標準ライブラリのスキルコレクション

## ディレクトリ構成

```
rust-std-skills/
├── SKILL.md              # メインエントリ（概要 + クイックリファレンス）
├── skills/               # サブモジュールスキル
│   ├── core-types/      # Option, Result, String, Box, Rc, Cell
│   ├── collections/     # Vec, HashMap, HashSet, BTreeMap など
│   ├── sync/            # Arc, Mutex, RwLock, Atomic*, thread, channels
│   ├── traits/          # Iterator, From/Into, Clone, Debug など
│   ├── io/              # Read, Write, BufReader, File, stdin/stdout
│   ├── system/          # fs, path, env, process, net, time
│   └── utilities/       # mem, fmt, error, marker traits
└── references/          # 詳細ドキュメント
    ├── _shared/         # 共有ルール (rust-defaults.md)
    ├── core-types/      # Option, Result, String, Box, Rc, Cell
    ├── collections/     # Vec, HashMap リファレンス
    ├── sync/            # Mutex, Arc, thread, mpsc, アトミック型
    ├── traits/          # 派生可能トレイト, 変換トレイト
    ├── io/              # I/O トレイト, ファイル操作
    ├── system/          # fs, path, env, process, net, time
    └── utilities/       # mem, fmt, error, marker
```

## 使用方法

### Claude Code スキルとして使用

このディレクトリを Claude Code スキルディレクトリにコピーまたはシンボリックリンク：

```bash
# 方法 1: シンボリックリンク
ln -s /path/to/rust-std-skills ~/.claude/skills/rust-std

# 方法 2: コピー
cp -r /path/to/rust-std-skills ~/.claude/skills/rust-std
```

### トリガーキーワード

以下のキーワードでスキルが有効化：
- `std::`, `標準ライブラリ`, `rust std`
- `Option`, `Result`, `String`, `Box`, `Rc`, `Cell`, `RefCell`
- `Vec`, `HashMap`, `HashSet`, `BTreeMap`
- `Arc`, `Mutex`, `RwLock`, `Atomic*`, `mpsc`, `thread`
- `Iterator`, `From`, `Into`, `Clone`, `Debug`, `Display`
- `Read`, `Write`, `File`, `BufReader`, `Path`, `PathBuf`
- `fs`, `env`, `process`, `Command`, `TcpListener`, `Duration`
- `Send`, `Sync`, `Copy`, `Drop`, `Sized`, `PhantomData`

## モジュール

| モジュール | 用途 | 主要型 |
|------------|------|--------|
| **core-types** | 基本型 | `Option`, `Result`, `String`, `Box`, `Rc`, `Cell` |
| **collections** | データ構造 | `Vec`, `HashMap`, `HashSet`, `BTreeMap`, `VecDeque` |
| **sync** | 並行処理プリミティブ | `Arc`, `Mutex`, `RwLock`, `Atomic*`, `thread`, `mpsc` |
| **traits** | コアトレイト | `Iterator`, `From/Into`, `Clone`, `Debug`, `Default` |
| **io** | 入出力 | `Read`, `Write`, `BufReader`, `File`, `stdin/stdout` |
| **system** | OS 操作 | `fs`, `path`, `env`, `process`, `net`, `time` |
| **utilities** | ユーティリティ | `mem`, `fmt`, `error`, `marker` |

## クイックリファレンス

### コア型
- `Option<T>` - オプション値 (Some/None)
- `Result<T, E>` - 成功/エラー結果
- `String` / `&str` - UTF-8 文字列
- `Box<T>` - ヒープ割り当て
- `Rc<T>` / `Arc<T>` - 参照カウント
- `Cell<T>` / `RefCell<T>` - 内部可変性

### コレクション
- `Vec<T>` - 可変長配列
- `HashMap<K, V>` - ハッシュマップ
- `HashSet<T>` - ユニーク値セット
- `BTreeMap<K, V>` - ソート済みマップ

### 並行処理
- `Arc<Mutex<T>>` - 共有可変状態
- `thread::spawn` - スレッド作成
- `mpsc::channel` - メッセージパッシング
- `Atomic*` - ロックフリープリミティブ

### システム
- `std::fs` - ファイル操作
- `std::path` - パス操作
- `std::env` - 環境変数
- `std::process` - プロセス管理
- `std::net` - TCP/UDP ネットワーク
- `std::time` - 時間処理

### コアトレイト
- `Clone` / `Copy` - 複製
- `Debug` / `Display` - フォーマット
- `From` / `Into` - 型変換
- `Iterator` - イテレーションプロトコル
- `Send` / `Sync` - スレッド安全性
- `Error` - エラー処理

## バージョン

- **Rust**: 1.92.0
- **Edition**: 2024
- **最終更新**: 2026-01-19
