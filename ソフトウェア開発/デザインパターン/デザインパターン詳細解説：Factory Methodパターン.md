---
title: デザインパターン詳細解説：Factory Method パターン
created: 2025-05-05 10:41:58
updated: 2025-05-05 12:22:11
draft: true
tags:
  - ソフトウェア設計
  - オブジェクト指向
  - デザインパターン
categories:
  - ソフトウェア設計
---

**目次**

- [デザインパターン詳細解説：Factory Method パターン](#デザインパターン詳細解説factory-method-パターン)
- [1. Factory Method パターンとは？ ～目的と解決したい問題～](#1-factory-method-パターンとは-目的と解決したい問題)
  - [1.1 このパターンを一言で言うと？（核心的な目的）](#11-このパターンを一言で言うと核心的な目的)
  - [1.2 なぜ Factory Method パターンが必要なのか？（動機と背景）](#12-なぜ-factory-method-パターンが必要なのか動機と背景)
    - [1.2.1 生成するクラスを固定したくない状況](#121-生成するクラスを固定したくない状況)
    - [1.2.2 インスタンス化の責任を分離・委譲したい](#122-インスタンス化の責任を分離委譲したい)
  - [1.3 このパターンで解決できること（メリットの要約）](#13-このパターンで解決できることメリットの要約)
- [2. パターンの構造と実装 ～どのように実現するか～](#2-パターンの構造と実装-どのように実現するか)
  - [2.1 登場人物とその役割（クラス図と解説）](#21-登場人物とその役割クラス図と解説)
  - [2.2 実装のポイント：生成の委譲とインターフェース依存](#22-実装のポイント生成の委譲とインターフェース依存)
  - [2.3 コード例：具体的なシナリオでの実装](#23-コード例具体的なシナリオでの実装)
    - [2.3.1 シナリオ設定（例：ログ出力ツールの切り替え）](#231-シナリオ設定例ログ出力ツールの切り替え)
    - [2.3.2 サンプルコード（Java での例）](#232-サンプルコードjava-での例)
    - [2.3.3 コードのポイント解説](#233-コードのポイント解説)
- [3. Factory Method パターンの利点 ～採用するメリット～](#3-factory-method-パターンの利点-採用するメリット)
  - [3.1 生成クラスの分離と疎結合](#31-生成クラスの分離と疎結合)
  - [3.2 拡張性の向上（OCP 準拠）](#32-拡張性の向上ocp-準拠)
  - [3.3 生成ロジックの柔軟性とサブクラスへの委譲](#33-生成ロジックの柔軟性とサブクラスへの委譲)
  - [3.4 生成知識の局所化](#34-生成知識の局所化)
- [4. 注意点とトレードオフ ～適用前に考えるべきこと～](#4-注意点とトレードオフ-適用前に考えるべきこと)
  - [4.1 クラス階層の増加と複雑性](#41-クラス階層の増加と複雑性)
  - [4.2 サブクラス化の必要性](#42-サブクラス化の必要性)
  - [4.3 クライアントの負担（Creator の選択）](#43-クライアントの負担creator-の選択)
  - [4.4 デバッグ時の間接性](#44-デバッグ時の間接性)
- [5. 実装上のヒントと考慮事項 ～より良く使うために～](#5-実装上のヒントと考慮事項-より良く使うために)
  - [5.1 `Creator` を抽象クラスにするか、具象クラスにするか](#51-creator-を抽象クラスにするか具象クラスにするか)
  - [5.2 `factoryMethod` のパラメータ活用](#52-factorymethod-のパラメータ活用)
  - [5.3 `Product` インターフェースの重要性](#53-product-インターフェースの重要性)
  - [5.4 命名規則](#54-命名規則)
  - [5.5 静的 (static) な Factory Method](#55-静的-static-な-factory-method)
- [6. 他のパターンとの関連 ～比較と組み合わせ～](#6-他のパターンとの関連-比較と組み合わせ)
  - [6.1 似ているパターンとの比較](#61-似ているパターンとの比較)
    - [6.1.1 Factory Method vs Abstract Factory](#611-factory-method-vs-abstract-factory)
    - [6.1.2 Factory Method vs Simple Factory (静的ファクトリメソッドとしばしば混同される)](#612-factory-method-vs-simple-factory-静的ファクトリメソッドとしばしば混同される)
    - [6.1.3 Factory Method vs Builder](#613-factory-method-vs-builder)
  - [6.2 組み合わせると効果的なパターン](#62-組み合わせると効果的なパターン)
- [7. リファクタリング：いつ Factory Method パターンを導入するか](#7-リファクタリングいつ-factory-method-パターンを導入するか)
  - [7.1 導入のきっかけとなる「コードの不吉な臭い」や状況変化](#71-導入のきっかけとなるコードの不吉な臭いや状況変化)
  - [7.2 段階的なリファクタリング手順（概要）](#72-段階的なリファクタリング手順概要)
- [8. まとめ ～ Factory Method パターンの本質～](#8-まとめ--factory-method-パターンの本質)

# デザインパターン詳細解説：Factory Method パターン

# 1. Factory Method パターンとは？ ～目的と解決したい問題～

## 1.1 このパターンを一言で言うと？（核心的な目的）

Factory Method パターンは、「**オブジェクトを生成するためのインターフェース（メソッド）だけをスーパークラスで定義し、どの具体的なクラスのインスタンスを生成するかの決定はサブクラスに任せる（遅延させる）**」ための、生成に関するデザインパターンです。インスタンス化の責任をサブクラスに移譲します。

## 1.2 なぜ Factory Method パターンが必要なのか？（動機と背景）

オブジェクト指向プログラミングでは、多くの場合、あるクラスが別のクラスのインスタンスを生成して利用します。最も単純な方法は `new ConcreteProduct()` のように直接コンストラクタを呼び出すことですが、この方法には柔軟性の面で問題が生じることがあります。

### 1.2.1 生成するクラスを固定したくない状況

以下のような状況では、オブジェクトを生成する側（クライアントやスーパークラス）が、生成されるオブジェクトの**具体的なクラス名をコード内に直接記述（ハードコーディング）したくない**、あるいは**できない**場合があります。

- **フレームワークとアプリケーション:** フレームワーク（スーパークラスに相当）は、アプリケーション（サブクラスに相当）が利用するであろうオブジェクトの「種類」（インターフェース）は知っていても、アプリケーションごとにカスタマイズされた「具体的な実装クラス」を知ることはできません。フレームワーク側で `new ConcreteApplicationObject()` と書くことはできないのです。
- **実行時の選択:** 生成するオブジェクトの種類が、設定ファイルの内容やユーザーの入力、あるいは実行時の条件によって動的に決まる場合。コンパイル時にはどのクラスを `new` すべきか確定できません。
- **依存関係の分離:** オブジェクトを利用するクラスが、具体的な生成クラスに直接依存してしまうと、両者の結合度が高くなります。将来、生成するクラスを変更したり、新しい種類を追加したりする場合に、利用側のクラスにも修正が必要となり、保守性が低下します。

### 1.2.2 インスタンス化の責任を分離・委譲したい

オブジェクトを「**利用する**」責任と、「**生成する**」責任は、本来異なる関心事です。これらの責任を分離することで、各クラスの責務が明確になり、より柔軟で変更に強い設計に繋がります。

特に、どの具体的なクラスをインスタンス化するかの**決定ロジック**を、利用側のクラスから切り離し、それを専門に担当する場所（このパターンの場合はサブクラス）に**委譲**したい、という要求があります。

Factory Method パターンは、これらの課題、すなわち「生成クラスへの直接依存」「実行時のクラス決定」「生成責任の分離」を解決するために考案されました。

## 1.3 このパターンで解決できること（メリットの要約）

Factory Method パターンを適用することで、以下のようなメリットが期待できます。

- オブジェクトの**生成処理をサブクラスに委譲**できる。
- 利用側コードは、生成される**具体的なクラス名への依存をなくし**、共通のインターフェース（`Product`）のみに依存できる（**疎結合**）。
- 新しい種類のオブジェクトを追加する際に、**既存の利用側コード（スーパークラス）を変更することなく**、新しいサブクラス（`ConcreteCreator` と `ConcreteProduct`）を追加するだけで対応できる（**OCP 準拠**）。
- オブジェクト生成に関する**知識（どのクラスをどう作るか）**を、それを必要とする場所（`ConcreteCreator`）に**局所化**できる。

---

# 2. パターンの構造と実装 ～どのように実現するか～

Factory Method パターンは、オブジェクト生成の責任をサブクラスに移譲するために、クラスの継承とメソッドのオーバーライドを利用します。

## 2.1 登場人物とその役割（クラス図と解説）

Factory Method パターンは、主に以下の 4 つの役割（クラスまたはインターフェース）から構成されます。

- **`Product`（製品インターフェース）:**
  - **役割:** Factory Method が生成するオブジェクトの**共通の型（インターフェースまたは抽象クラス）**を定義します。`Creator` はこの `Product` 型を通じて生成されたオブジェクトを利用します。
  - **定義:** 生成されるオブジェクトが共通して持つべきメソッドを宣言します。
- **`ConcreteProduct`（具体的な製品）:**
  - **役割:** `Product` インターフェースを**実装する具体的なクラス**です。Factory Method によって実際に生成されるオブジェクトです。
  - **実装:** `Product` インターフェースで宣言されたメソッドを実装します。
- **`Creator`（作成者クラス/インターフェース）:**
  - **役割:** `Product` オブジェクトを生成するためのメソッド、すなわち **`factoryMethod()` を宣言**します。このメソッドの戻り値の型は `Product` です。
  - **実装:**
    - `factoryMethod()` は、**抽象メソッド**として宣言されるか（サブクラスでの実装を強制）、あるいは**デフォルトの `ConcreteProduct` を返す具象メソッド**として実装されることもあります。
    - 多くの場合、`Creator` は自身で `factoryMethod()` を呼び出して `Product` オブジェクトを取得し、それを利用する何らかの処理（例: `someOperation()`）を持ちます。この処理は `Product` インターフェースにのみ依存します。
- **`ConcreteCreator`（具体的な作成者）:**
  - **役割:** `Creator` を継承（または実装）し、**`factoryMethod()` をオーバーライド**して、**特定の `ConcreteProduct` を生成**する責任を持ちます。
  - **実装:** オーバーライドした `factoryMethod()` 内で、対応する `ConcreteProduct` のインスタンスを `new` して返します。「どの製品を作るか」という知識は、このクラスにカプセル化されます。

```mermaid
classDiagram
    class Product {
        <<interface>>
        + operation()
    }
    class ConcreteProductA {
        + operation()
    }
    class ConcreteProductB {
        + operation()
    }
    class Creator {
        <<abstract>>
        # factoryMethod(): Product*  // Factory Method (abstract or default impl)
        + someOperation() // Uses the product created by factoryMethod
                        // e.g., { Product p = factoryMethod(); p.operation(); }
    }
    class ConcreteCreatorA {
        + factoryMethod(): Product // Overrides to return new ConcreteProductA()
    }
    class ConcreteCreatorB {
        + factoryMethod(): Product // Overrides to return new ConcreteProductB()
    }

    ConcreteProductA ..|> Product : implements
    ConcreteProductB ..|> Product : implements
    Creator --> Product : uses / creates via factoryMethod
    ConcreteCreatorA --|> Creator : extends
    ConcreteCreatorB --|> Creator : extends
    ConcreteCreatorA ..> ConcreteProductA : creates
    ConcreteCreatorB ..> ConcreteProductB : creates

    note for Creator "factoryMethod()を宣言。Productを利用する処理も持つことが多い。"
    note for ConcreteCreatorA "factoryMethod()を実装し、具体的な製品Aを生成する責任を持つ。"
    note for Product "生成されるオブジェクトの共通インターフェース。"
```

_図: Factory Method パターンのクラス図_

## 2.2 実装のポイント：生成の委譲とインターフェース依存

- **生成責任の委譲:** このパターンの核心は、`new ConcreteProduct()` という**具体的なクラスのインスタンス化を行う責任**を、`Creator` から `ConcreteCreator` の `factoryMethod()` へと**委譲（遅延）**する点にあります。
- **インターフェースへの依存:** `Creator` クラス（および多くの場合クライアントも）は、具体的な `ConcreteProduct` クラスを知る必要がなく、抽象的な `Product` **インターフェースにのみ依存**します。これにより、`Creator` のコードを変更することなく、生成される `Product` の種類を `ConcreteCreator` を切り替えることで変更できます。
- **`factoryMethod` の実装:**
  - 最も一般的なのは、`Creator` で `factoryMethod` を `abstract` とし、`ConcreteCreator` でオーバーライドして実装する方法です。
  - `Creator` で `factoryMethod` にデフォルト実装（例えば、デフォルトの `ConcreteProduct` を返す）を持たせ、必要に応じて `ConcreteCreator` でオーバーライドすることも可能です。
  - `factoryMethod` は、単に `new` するだけでなく、オブジェクトプールからインスタンスを返すなど、より複雑な生成ロジックを含むこともできます。
- **パラメータ化された Factory Method:** `factoryMethod` に引数を渡し、その引数に応じて生成する `ConcreteProduct` の種類を変える、という応用も可能です。（ただし、これが複雑になりすぎる場合は、他の生成パターン（Abstract Factory や Builder）を検討する方が良いかもしれません）

## 2.3 コード例：具体的なシナリオでの実装

### 2.3.1 シナリオ設定（例：ログ出力ツールの切り替え）

アプリケーションのログを出力する機能を考えます。ログの出力先として、コンソールとファイルの両方をサポートし、設定によって切り替えられるようにしたいとします。ログを出力する処理自体は共通化したいと考えます。

### 2.3.2 サンプルコード（Java での例）

```java
// 1. Product インターフェース: ログ出力機能
interface Logger {
    void log(String message);
}

// 2. ConcreteProduct クラス: 具体的なロガー
class ConsoleLogger implements Logger {
    @Override public void log(String message) {
        System.out.println("CONSOLE: " + message);
    }
}
class FileLogger implements Logger {
    private String filePath;
    public FileLogger(String filePath) { this.filePath = filePath; }
    @Override public void log(String message) {
        // 実際はファイルに書き込む処理
        System.out.println("FILE(" + filePath + "): " + message);
    }
}
// 新しいロガー（例: DatabaseLogger）はここに追加

// 3. Creator 抽象クラス: ロガー生成と利用の骨組み
abstract class LoggerFactory {
    // Logger を利用する共通処理
    public void writeLog(String message) {
        Logger logger = createLogger(); // ★ Factory Method で Logger を取得
        // 取得した Logger を使ってログ出力
        logger.log("Info: " + message);
    }

    // ★ Factory Method (サブクラスで実装)
    protected abstract Logger createLogger();
}

// 4. ConcreteCreator クラス: 具体的なロガーを生成
class ConsoleLoggerFactory extends LoggerFactory {
    @Override
    protected Logger createLogger() {
        // ConsoleLogger を生成して返す
        System.out.println("(Creating ConsoleLogger)");
        return new ConsoleLogger();
    }
}
class FileLoggerFactory extends LoggerFactory {
    private String filePath;
    public FileLoggerFactory(String filePath) { this.filePath = filePath; }

    @Override
    protected Logger createLogger() {
        // FileLogger を生成して返す
        System.out.println("(Creating FileLogger for " + filePath + ")");
        return new FileLogger(filePath);
    }
}
// 新しいロガー用の Factory (例: DatabaseLoggerFactory) はここに追加

// --- Client (利用側) ---
public class FactoryMethodClient {
    public static void main(String[] args) {
        LoggerFactory factory;
        // 実行時や設定に応じて Factory を選択
        boolean logToFile = true; // 設定フラグ (例)

        if (logToFile) {
            factory = new FileLoggerFactory("/var/log/app.log");
        } else {
            factory = new ConsoleLoggerFactory();
        }

        // Client は LoggerFactory の writeLog を呼び出すだけ。
        // 内部でどの Logger が使われるかは意識しない。
        factory.writeLog("Application started.");
        factory.writeLog("Processing data...");
        factory.writeLog("Application finished.");

        // もし DatabaseLogger を追加しても、Client のコード変更は不要。
        // LoggerFactory databaseFactory = new DatabaseLoggerFactory(...);
        // databaseFactory.writeLog("...");
    }
}
```

### 2.3.3 コードのポイント解説

- `Logger` が `Product` インターフェース、`ConsoleLogger` と `FileLogger` が `ConcreteProduct` です。
- `LoggerFactory` が `Creator` 抽象クラスで、`createLogger()` が **Factory Method** です。`writeLog()` メソッドは `Product` を利用する共通処理です。
- `ConsoleLoggerFactory` と `FileLoggerFactory` が `ConcreteCreator` で、それぞれ対応する `ConcreteProduct` を生成する `createLogger()` を実装しています。
- クライアント (`FactoryMethodClient`) は、利用したい `LoggerFactory` (サブクラス) を選択し、その `writeLog()` を呼び出します。クライアントは具体的な `Logger` クラスを知る必要がなく、`LoggerFactory` のインターフェースのみに依存しています。

このように、Factory Method パターンはオブジェクト生成の責任をサブクラスに委譲することで、システムの柔軟性と拡張性を高めます。

---

# 3. Factory Method パターンの利点 ～採用するメリット～

Factory Method パターンを適用することで、オブジェクト生成に関する設計が改善され、多くのメリットが得られます。

## 3.1 生成クラスの分離と疎結合

このパターンの最も重要なメリットは、**オブジェクトを利用するコード（`Creator` やクライアント）と、実際に生成される具体的なオブジェクト（`ConcreteProduct`）とを分離できる**ことです。

- `Creator` は、具体的な `ConcreteProduct` クラスの名前を知る必要がなく、抽象的な `Product` **インターフェースにのみ依存**します。
- どの `ConcreteProduct` を生成するかの決定は、`ConcreteCreator` の `factoryMethod` 内にカプセル化されます。

これにより、`Creator` と `ConcreteProduct` の間の**結合度が低下（疎結合）**し、互いに独立して変更・拡張することが容易になります。例えば、`ConcreteProduct` の実装詳細が変更されても、`Product` インターフェースが変わらなければ、`Creator` 側のコードは影響を受けません。

## 3.2 拡張性の向上（OCP 準拠）

Factory Method パターンは、**オープン/クローズドの原則 (OCP)** を満たすのに役立ちます。新しい種類の製品 (`ConcreteProduct`) をシステムに追加したい場合、通常は以下の手順で対応できます。

1.  新しい `ConcreteProduct` クラスを作成し、`Product` インターフェースを実装します。
2.  その新しい製品を生成するための新しい `ConcreteCreator` クラスを作成し、`factoryMethod` をオーバーライドして新しい製品を返すようにします。

この際、**既存の `Creator` スーパークラスや、他の `ConcreteCreator`、`ConcreteProduct`、そしてクライアントコードを修正する必要は（基本的には）ありません**。新しいクラスを**追加**するだけで、システムの機能を**拡張**できるのです。これは、変更に強く、保守しやすいソフトウェア設計の重要な特性です。

## 3.3 生成ロジックの柔軟性とサブクラスへの委譲

オブジェクトの生成プロセスは、単に `new` 演算子を呼び出すだけとは限りません。場合によっては、初期化パラメータの設定、オブジェクトプールからの取得、Singleton インスタンスの返却など、より複雑なロジックが必要になることもあります。

Factory Method パターンでは、この**生成ロジック全体を `ConcreteCreator` の `factoryMethod` 内にカプセル化**できます。これにより、生成プロセスが複雑であっても、その詳細を利用側のコードから隠蔽することができます。

また、どのクラスを生成するかという**決定権をサブクラスに委譲**できるため、フレームワークやライブラリのように、基本的な構造を提供しつつ、具体的な部分（生成するオブジェクトの種類）は利用者にカスタマイズさせたい、といった場合に非常に有効なパターンとなります。

## 3.4 生成知識の局所化

「どのクラスを、どのように生成するか」という知識は、ソフトウェア全体に分散させるのではなく、特定の場所に集約する方が管理しやすくなります。Factory Method パターンでは、特定の `ConcreteProduct` を生成するための知識は、対応する `ConcreteCreator` の `factoryMethod` 内に**局所化**されます。

これにより、オブジェクトの生成方法に変更があった場合でも、修正箇所を特定しやすくなり、コード全体の保守性が向上します。

これらの利点により、Factory Method パターンは、オブジェクト生成の柔軟性と拡張性を高めたい場合に、非常に有用な設計パターンとなります。

---

# 4. 注意点とトレードオフ ～適用前に考えるべきこと～

Factory Method パターンはオブジェクト生成に柔軟性をもたらしますが、その適用にはいくつかの注意点と考慮すべきトレードオフが存在します。

## 4.1 クラス階層の増加と複雑性

Factory Method パターンを導入する際の最も顕著なトレードオフは、**クラス階層が深くなり、システム全体のクラス数が増加する**傾向があることです。

- 新しい種類の製品 (`ConcreteProduct`) を追加するたびに、それに対応する**`ConcreteCreator` サブクラスも通常はセットで作成**する必要があります。
- これにより、`Creator` を中心としたクラス階層が形成され、管理すべきクラスの数が増えます。

もし、生成するオブジェクトの種類が少なく、将来的に増える可能性も低い、あるいは生成ロジックが非常に単純な場合にまで Factory Method パターンを適用すると、**過剰設計**となり、かえってシステムの構造を**不必要に複雑にしてしまう**可能性があります。シンプルな `if-else` や `switch` による生成の方が、結果的に分かりやすい場合もあります。

## 4.2 サブクラス化の必要性

Factory Method パターンの基本的な実装は、**`Creator` クラスを継承してサブクラス (`ConcreteCreator`) を作成する**ことを前提としています。

- もし、対象となる `Creator` に相当するクラスが既に別の目的で継承を使っている場合（多重継承が許されない言語の場合）、このパターンを直接適用するのが難しいことがあります。
- 既存のクラス階層に Factory Method を後から導入する場合、クラス構造の変更が必要になることがあります。

ただし、`Creator` をインターフェースとして定義し、`factoryMethod` もインターフェースのメソッドとするアプローチも考えられます。この場合、`ConcreteCreator` はインターフェースを実装する形になります。

## 4.3 クライアントの負担（Creator の選択）

`Creator` やクライアントコードは、生成される具体的な `Product` クラスを知る必要はなくなりますが、代わりに**どの `ConcreteCreator` を利用するかを選択する**必要が出てきます。

```java
// Client はどの Factory を使うかを知る必要がある
LoggerFactory factory;
if (logToFile) {
    factory = new FileLoggerFactory("/var/log/app.log"); // ★ここでの選択
} else {
    factory = new ConsoleLoggerFactory(); // ★ここでの選択
}
factory.writeLog("...");
```

この「どの `ConcreteCreator` を使うか」という選択ロジックがクライアント側に現れることになります。これが複雑になる場合は、さらに別の生成パターン（例えば Abstract Factory や、設定ファイルと DI コンテナを組み合わせるなど）を検討する必要があるかもしれません。

## 4.4 デバッグ時の間接性

オブジェクトが生成される箇所が、直接的な `new` 呼び出しではなく、`factoryMethod` の呼び出しを経由するため、デバッグ時にオブジェクトが**実際にどこで生成されたのかを追跡するのが、少しだけ間接的に**なります。特に、`Creator` の階層が深かったり、`factoryMethod` の実装が複雑だったりすると、生成プロセスを追うのが若干難しく感じることがあるかもしれません。

これらの注意点を理解し、Factory Method パターンを導入するメリット（疎結合、拡張性、柔軟性）が、これらのトレードオフ（クラス数増加、サブクラス化の必要性など）を上回るかどうかを、具体的な設計状況や将来の見通しに基づいて慎重に判断することが重要です。

---

# 5. 実装上のヒントと考慮事項 ～より良く使うために～

Factory Method パターンを実装する際には、その柔軟性を活かしつつ、分かりやすく保守しやすいコードにするために、いくつかのヒントや考慮事項があります。

## 5.1 `Creator` を抽象クラスにするか、具象クラスにするか

`Creator` をどのように定義するかは、主に 2 つのアプローチがあります。

- **`Creator` を抽象クラス (Abstract Class) にする:**
  - `factoryMethod()` を**抽象メソッド (`abstract`)** として宣言します。
  - これにより、サブクラス (`ConcreteCreator`) に対して `factoryMethod` の**実装を強制**することができます。
  - `Creator` 自身はインスタンス化できません。サブクラスの利用が前提となります。
  - 最も一般的で、パターンの意図を明確に示す方法です。
- **`Creator` を具象クラス (Concrete Class) にする:**
  - `factoryMethod()` に**デフォルトの実装**を提供します（例えば、デフォルトの `ConcreteProduct` を生成して返す）。
  - サブクラスは、必要に応じて `factoryMethod` を**オーバーライド**して、生成するオブジェクトを変更できます。オーバーライドしなければ、デフォルトの製品が生成されます。
  - `Creator` クラス自身もインスタンス化して利用できます。
  - サブクラスを作る必要がない単純なケースや、デフォルトの振る舞いを提供したい場合に有効です。

どちらを選択するかは、デフォルトの製品を提供したいか、サブクラスに実装を強制したいか、といった要求によります。

## 5.2 `factoryMethod` のパラメータ活用

`factoryMethod` に**引数**を持たせることで、生成する `ConcreteProduct` の種類や初期状態を、より動的に決定することも可能です。

```java
// パラメータ化された Factory Method の例
abstract class ShapeFactory {
    public abstract Shape createShape(String shapeType); // 引数で種類を指定
}

class SimpleShapeFactory extends ShapeFactory {
    @Override
    public Shape createShape(String shapeType) {
        if ("Circle".equalsIgnoreCase(shapeType)) {
            return new Circle();
        } else if ("Rectangle".equalsIgnoreCase(shapeType)) {
            return new Rectangle();
        }
        // ...
        return null; // またはデフォルトShapeや例外
    }
}
```

ただし、`factoryMethod` の引数による分岐ロジックが非常に複雑になる場合は、パターンが意図する「サブクラスによる決定」という原則から外れてしまう可能性があります。そのような場合は、サブクラスを分ける、あるいは他の生成パターン（Abstract Factory や Builder など）を検討する方が良いかもしれません。

## 5.3 `Product` インターフェースの重要性

`Creator` やクライアントコードが、具体的な `ConcreteProduct` クラスではなく、抽象的な `Product` **インターフェース（または抽象クラス）に依存する**ことが、このパターンの疎結合性を実現する上での鍵です。

`Product` インターフェースは、生成されるすべてのオブジェクトが共通して持つべき操作を明確に定義する必要があります。これにより、`Creator` は `factoryMethod` がどの `ConcreteProduct` を返したとしても、`Product` インターフェースを通じて一貫した方法でオブジェクトを利用できます。

## 5.4 命名規則

- **Factory Method:** 生成メソッドの名前は、`create<Product>`、`make<Product>`、`build<Product>`、あるいは単に `create` や `getInstance` など、生成するものであることが明確に分かる名前が良いでしょう。`get<Product>` のような名前は、既存のインスタンスを返すゲッターと混同しやすいため、避けた方が無難な場合があります。
- **Creator:** 作成者クラスの名前は、`〇〇Creator` や `〇〇Factory` といった命名規則が一般的です。

一貫性のある命名規則を採用することで、コードを読む人がパターンの意図を理解しやすくなります。

## 5.5 静的 (static) な Factory Method

場合によっては、`Creator` をインスタンス化せずに、**`static` な Factory Method** を直接呼び出す形式も使われます。

```java
// Static Factory Method の例
class LoggerProvider {
    public static Logger createLogger(String type) { // static メソッド
        if ("console".equals(type)) {
            return new ConsoleLogger();
        } else if ("file".equals(type)) {
            return new FileLogger("app.log");
        }
        // ...
        throw new IllegalArgumentException("Unknown logger type");
    }
}
// Client
Logger logger = LoggerProvider.createLogger("console");
logger.log("message");
```

この方法はサブクラス化を伴わないため、厳密には GoF の Factory Method パターンとは少し異なりますが、「生成ロジックをカプセル化し、クライアントから具体的なクラス名を隠蔽する」という目的は共通しています。『Effective Java』などでは、コンストラクタの代わりに static ファクトリメソッドを使うことの利点も述べられています（名前を持てる、戻り値の型を柔軟にできる、など）。

これらのヒントを参考に、Factory Method パターンを状況に合わせて適切に実装し、オブジェクト生成の柔軟性と保守性を高めていきましょう。

---

# 6. 他のパターンとの関連 ～比較と組み合わせ～

Factory Method パターンは、他のデザインパターン、特に生成に関するパターンや、Template Method パターンと関連があります。これらのパターンとの違いや連携方法を理解することで、より適切な設計判断が可能になります。

## 6.1 似ているパターンとの比較

### 6.1.1 Factory Method vs Abstract Factory

この 2 つはどちらもオブジェクト生成を扱いますが、そのスコープと目的が異なります。

- **Factory Method:**
  - **スコープ:** 通常、**1 種類**の製品 (`Product`) オブジェクトの生成を扱います。
  - **目的:** オブジェクト生成の**プロセス**（どの `ConcreteProduct` を `new` するか）を**サブクラスに委譲**することに焦点を当てます。継承を利用するのが特徴です。
- **Abstract Factory:**
  - **スコープ:** **互いに関連する複数の種類**の製品オブジェクト群（**製品ファミリー**）をまとめて生成することを扱います。（例: ある UI テーマに属するボタン、テキストボックス、ウィンドウのセット）
  - **目的:** クライアントが具体的な製品クラスを知ることなく、**特定のファミリーに属するオブジェクト群を整合性を保って生成**するためのインターフェースを提供することに焦点を当てます。通常、委譲（コンポジション）を利用します。
- **見分け方:** 生成したいものが**単一の種類**で、その生成方法をサブクラスで変えたいなら `Factory Method`。**関連する部品一式**をセットで生成したいなら `Abstract Factory` を検討します。しばしば、Abstract Factory のインターフェースで宣言された各部品生成メソッドが、内部的に Factory Method として実装されることもあります。

### 6.1.2 Factory Method vs Simple Factory (静的ファクトリメソッドとしばしば混同される)

- **Simple Factory (または Static Factory Method):** 前章で触れたように、クラスの `static` メソッド内で条件分岐などを使ってインスタンスを生成し返す方法。サブクラス化を伴いません。
- **Factory Method (GoF):** スーパークラスで生成メソッドのインターフェースを定義し、サブクラスで具体的な生成を実装する、**継承ベース**のパターンです。
- **違い:** Simple Factory は単純ですが、新しい製品を追加する際に Factory メソッド自体の修正が必要になり、OCP に反します。Factory Method はサブクラスを追加するだけで拡張できるため、より柔軟性があります。

### 6.1.3 Factory Method vs Builder

- **違い:** Factory Method は「**どのクラスを作るか**」の決定を分離・遅延させることに焦点を当てます。Builder は「**どのように複雑なオブジェクトを組み立てるか**」という構築プロセスに焦点を当てます。Builder は多くのパーツから成るオブジェクトの生成に適しています。

## 6.2 組み合わせると効果的なパターン

Factory Method パターンは、他のパターンと組み合わせて使われることもよくあります。

- **Template Method パターン:**
  - **連携:** Template Method パターンで定義されたアルゴリズムの骨組みの中で、**特定のステップで必要となるオブジェクトを生成する**ために Factory Method を利用することができます。テンプレートメソッドが `factoryMethod()` を呼び出し、サブクラスがそのステップで使用する具体的なオブジェクトを提供します。
- **Prototype パターン:**
  - **連携:** `ConcreteCreator` の `factoryMethod` 内で、`new` を使ってインスタンスを生成する代わりに、あらかじめ用意された**プロトタイプオブジェクトを `clone()`** して返すことも可能です。これは、オブジェクトの生成コストが高い場合や、特定の初期状態を持つインスタンスを複製したい場合に有効です。
- **Singleton パターン:**
  - **連携:** `factoryMethod` が返すオブジェクトが、システム全体で唯一のインスタンスであるべき場合、`factoryMethod` は **Singleton パターン**で管理されているインスタンスを返すように実装できます。

これらのパターンとの関係性を理解することで、Factory Method パターンを単独で適用するだけでなく、他のパターンと効果的に連携させ、より洗練された設計を構築することができます。

---

# 7. リファクタリング：いつ Factory Method パターンを導入するか

Factory Method パターンは、最初から設計に組み込まれることもありますが、既存のコードがオブジェクト生成に関して柔軟性を欠いている場合に、**リファクタリング**によって後から導入されることも効果的です。

## 7.1 導入のきっかけとなる「コードの不吉な臭い」や状況変化

既存のコードベースに以下のような兆候や状況の変化が見られた場合、Factory Method パターンの導入を検討する良いサインです。

- **コンストラクタ呼び出しの条件分岐:**
  - **症状:** あるクラス（`Creator` に相当するクラス）のメソッド内で、特定の条件（パラメータの値、設定、オブジェクトの型など）に基づいて、**`new ConcreteProductA()` や `new ConcreteProductB()` のように、生成する具象クラスを `if-else` や `switch` で切り替えている**箇所がある。
  - **問題:** 生成ロジックが利用側のコードに直接埋め込まれており、新しい種類の `Product` を追加するたびに、この条件分岐を修正する必要がある（OCP 違反）。`Creator` が具体的な `ConcreteProduct` クラスに依存してしまっている。
  - **解決策:** Factory Method パターンを導入します。条件分岐による生成ロジックを `factoryMethod` として抽出し、可能であれば条件ごとに `ConcreteCreator` サブクラスを作成して、各サブクラスが対応する `ConcreteProduct` を生成するように責務を移譲します。
- **生成ロジックの重複:**
  - **症状:** 類似したオブジェクト生成のロジック（特定のパラメータでの初期化などを含む）が、コードベースの複数の場所に重複して現れている。
  - **問題:** コードの重複は保守性の低下を招きます。生成方法に変更があった場合に、すべての箇所を修正する必要があります。
  - **解決策:** Factory Method パターンを導入し、生成ロジックを `factoryMethod` 内に集約します。利用側は Factory Method を呼び出すだけで済むようになります。
- **具体的な生成クラスへの依存を避けたい:**
  - **症状:** ライブラリやフレームワークの一部としてクラスを設計しており、利用する側（サブクラスやクライアント）に、生成するオブジェクトの具体的な種類をカスタマイズさせたいが、現在の実装ではスーパークラスが具象クラスを直接 `new` してしまっている。
  - **問題:** 拡張性が低く、利用者が柔軟にカスタマイズできません。
  - **解決策:** Factory Method パターンを導入し、オブジェクト生成部分を `factoryMethod` として定義します。利用者は、`Creator` を継承して `factoryMethod` をオーバーライドすることで、生成するオブジェクトを自由に変更できるようになります。

これらの状況は、オブジェクトの生成と利用の間の結合度が高すぎる、あるいは生成ロジックが適切に分離・カプセル化されていない可能性を示唆しており、Factory Method パターンによるリファクタリングが有効な場合があります。

## 7.2 段階的なリファクタリング手順（概要）

既存のコードに Factory Method パターンを導入する際の、一般的なリファクタリング手順の概要は以下の通りです。（テストによる安全確保が前提です）

1. **`Product` インターフェース（または抽象クラス）の準備:**
   - 生成されるオブジェクト群に共通のインターフェース (`Product`) がまだ存在しない場合は、**インターフェースの抽出 (Extract Interface)** または **スーパークラスの抽出 (Extract Superclass)** を行い、共通の型を定義します。既存の `ConcreteProduct` クラスがこのインターフェースを実装するように修正します。
2. **`Creator` クラスの準備:**
   - オブジェクトを生成している既存のクラスを `Creator`（またはそのサブクラス）とします。必要であれば、`Creator` のための抽象クラスやインターフェースを定義します。
3. **`factoryMethod` の導入:**
   - `Creator` クラス（またはインターフェース）に、`Product` 型を返す `factoryMethod`（例: `createProduct()`）を宣言します（最初は `abstract` か、既存の生成ロジックを持つ具象メソッド）。
   - `Creator` 内で `new ConcreteProduct()` を直接呼び出していた箇所を、`factoryMethod()` の呼び出しに置き換えます。
4. **(オプション) `ConcreteCreator` の作成とロジック移動:**
   - もし `Creator` 内の `factoryMethod` (または元の生成箇所) に、生成するクラスを決定するための条件分岐が含まれている場合、その条件分岐の各ケースに対応する `ConcreteCreator` サブクラスを作成することを検討します。
   - 各 `ConcreteCreator` の `factoryMethod` をオーバーライドし、対応する `ConcreteProduct` を `new` して返すロジックを**移動 (Move Method)** します。
   - 元の `Creator` の `factoryMethod` は `abstract` にするか、デフォルト実装を残します。
5. **クライアントコードの修正:**
   - クライアントが `Creator` を利用する際に、必要に応じて適切な `ConcreteCreator` をインスタンス化するように修正します。（場合によっては、クライアントの変更が不要なこともあります）
6. **テスト:** 各ステップの後、および最終的に、テストを実行してリファクタリングによって外部から見た振る舞いが変わっていないこと、そして意図した `ConcreteProduct` が生成されることを確認します。

このリファクタリングプロセスにより、オブジェクトの生成ロジックが整理され、利用側のコードは具体的な生成クラスから独立し、システムの柔軟性と拡張性が向上します。

---

# 8. まとめ ～ Factory Method パターンの本質～

**Factory Method パターン**は、**オブジェクトを生成するためのインターフェース（メソッド）を定義**し、**どの具体的なクラスのインスタンスを生成するかの決定をサブクラスに委譲（遅延）** する、生成に関する重要なデザインパターンです。

このパターンを適用することで、

- オブジェクトの**利用側と生成側を分離**し、**疎結合**な設計を実現できる。
- 生成される**具体的なクラスへの依存を排除**し、インターフェースへの依存を促進できる。
- 新しい種類のオブジェクトを追加する際に、**既存コードへの影響を最小限**に抑え、**拡張性**を高めることができる（OCP 準拠）。
- オブジェクト**生成に関するロジックをサブクラスに集約・カプセル化**できる。

といったメリットが得られます。

その本質は、**インスタンス化の責任をサブクラスに移譲**することによる「**仮想コンストラクタ**」のような役割、あるいは「**オブジェクト生成のサブクラス化**」と言えます。スーパークラスは「何らかの製品を作る」という契約（メソッド）だけを定義し、サブクラスが「具体的にどの製品を作るか」を決定します。

ただし、クラス階層が増加することによる**複雑性の増加**や、**サブクラス化の必要性**といった**トレードオフ**も存在するため、適用は状況に応じて慎重に判断する必要があります。

Factory Method パターンは、フレームワークやライブラリの設計において、拡張性や柔軟性を提供するための基本的なテクニックとして広く用いられています。オブジェクト生成のプロセスに柔軟性を持たせたい、あるいは生成するクラスと利用するクラスを分離したいと考えた場合に、このパターンは非常に有効な選択肢となるでしょう。
