---
title: 仮想環境(venv)
created: 2025-09-29 14:12:03
updated: 2025-09-29 14:13:07
tags: []
---

# 仮想環境 (venv)

## 1. 仮想環境とは

仮想環境（Virtual Environment）とは、Python プロジェクトごとに独立した Python 実行環境を構築するための仕組みです。これにより、プロジェクト A で必要なライブラリのバージョンと、プロジェクト B で必要なライブラリのバージョンが異なる場合でも、互いに干渉することなく開発を進めることができます。

### なぜ仮想環境を使うのか？

- **依存関係の分離**: プロジェクトごとに必要なライブラリとそのバージョンを独立して管理できます。
- **グローバル環境の汚染防止**: システム全体にインストールされている Python 環境を、特定のプロジェクトのライブラリで汚染するのを防ぎます。
- **再現性**: `requirements.txt` ファイルと組み合わせることで、他の開発者やデプロイ環境でも同じ依存関係を簡単に再現できます。

## 2. `venv` の基本的な使い方

Python 3.3 以降では、標準ライブラリとして `venv` モジュールが提供されており、追加のインストールなしで仮想環境を作成できます。

### 2.1. 仮想環境の作成

プロジェクトのルートディレクトリで以下のコマンドを実行します。`env_name` は仮想環境の名前で、一般的には `venv` や `env` とします。

```bash
python -m venv env_name
```

例:

```bash
python -m venv .venv
```

これにより、`.venv` という名前のディレクトリが作成され、その中に独立した Python インタープリタと `pip` がインストールされます。

### 2.2. 仮想環境のアクティベート（有効化）

仮想環境を使用するには、それを「アクティベート（有効化）」する必要があります。アクティベートすると、その仮想環境の Python と `pip` が優先的に使用されるようになります。

- **Windows (Command Prompt)**:

  ```bash
  .\env_name\Scripts\activate
  ```

  例:

  ```bash
  .\.venv\Scripts\activate
  ```

- **Windows (PowerShell)**:

  ```bash
  .\env_name\Scripts\Activate.ps1
  ```

  例:

  ```bash
  .\.venv\Scripts\Activate.ps1
  ```

- **Linux / macOS (Bash/Zsh)**:
  ```bash
  source env_name/bin/activate
  ```
  例:
  ```bash
  source .venv/bin/activate
  ```

アクティベートが成功すると、コマンドラインのプロンプトの先頭に仮想環境の名前（例: `(.venv)`）が表示されます。

### 2.3. 仮想環境のデアクティベート（無効化）

仮想環境の使用を終了し、システムのグローバルな Python 環境に戻るには、以下のコマンドを実行します。

```bash
deactivate
```

### 2.4. 仮想環境の削除

仮想環境は単なるディレクトリなので、不要になった場合はそのディレクトリを削除するだけで OK です。

```bash
# Windows
rmdir /s /q env_name

# Linux / macOS
rm -rf env_name
```

例:

```bash
# Windows
rmdir /s /q .venv

# Linux / macOS
rm -rf .venv
```

## 3. `requirements.txt` との連携

`requirements.txt` ファイルは、プロジェクトが依存するすべての Python ライブラリとそのバージョンを記録するための標準的な方法です。仮想環境と組み合わせることで、プロジェクトの依存関係を簡単に管理・再現できます。

### 3.1. インストールされているライブラリの記録

仮想環境がアクティベートされた状態で、現在インストールされているすべてのライブラリを `requirements.txt` に出力します。

```bash
pip freeze > requirements.txt
```

### 3.2. `requirements.txt` からのライブラリのインストール

新しい環境でプロジェクトを開始する際や、他の開発者がプロジェクトに参加する際に、`requirements.txt` に記述されたライブラリを一括でインストールできます。

```bash
pip install -r requirements.txt
```

## 4. よくある問題と解決策

- **`activate` スクリプトが見つからない/実行できない**:
  - 仮想環境を作成したディレクトリ名が正しいか確認してください。
  - Windows の PowerShell で `Activate.ps1` が実行できない場合、スクリプトの実行ポリシーが制限されている可能性があります。管理者権限で PowerShell を開き、`Set-ExecutionPolicy RemoteSigned` を実行してポリシーを変更する必要があるかもしれません（セキュリティリスクを理解した上で実行してください）。
- **仮想環境をアクティベートしたのに、グローバルな Python が使われている**:
  - アクティベートコマンドが正しく実行されたか、プロンプトに仮想環境名が表示されているか確認してください。
  - `which python` (Linux/macOS) や `where python` (Windows) を実行して、使用されている Python のパスが仮想環境内のものになっているか確認してください。
- **`pip install` してもグローバル環境にインストールされてしまう**:
  - 仮想環境がアクティベートされているか確認してください。アクティベートされていないと、システム全体の Python 環境にインストールされてしまいます。
