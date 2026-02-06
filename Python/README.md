# Python ベストプラクティス

**対象読者**  
高校卒業したばかりで、ITに知見がない方が、技術書として読み進められることを想定しています。

**目的**  
Python で「読みやすく」「ミスが少なく」「保守しやすい」コードを書くための考え方を、章ごとに学べるようにまとめました。

---

## Python とは？

**Python** は、**読みやすさを重視したプログラミング言語**です。

- シンプルな文法で、初心者にも理解しやすい  
- Webアプリ、データ分析、自動化など、幅広い用途で使われる  
- 「インデント（字下げ）」でブロックを表すのが特徴  

---

## 目次

| 章 | ファイル | 内容 |
|----|----------|------|
| **第1章** | [01-basics.md](./01-basics.md) | Python とは・基本の書き方（変数、出力、コメント） |
| **第2章** | [02-data-types.md](./02-data-types.md) | データ型（文字列、数値、リスト、辞書） |
| **第3章** | [03-functions.md](./03-functions.md) | 関数の書き方と使い方 |
| **第4章** | [04-best-practices.md](./04-best-practices.md) | ベストプラクティスまとめ |
| **第5章** | [05-pep8-style.md](./05-pep8-style.md) | PEP 8 とスタイル（フォーマッター） |
| **第6章** | [06-virtual-env.md](./06-virtual-env.md) | 仮想環境と requirements.txt |
| **第7章** | [07-type-hints.md](./07-type-hints.md) | 型ヒント |
| **第8章** | [08-comprehensions-generators.md](./08-comprehensions-generators.md) | リスト内包表記・ジェネレータ・with 文 |
| **第9章** | [09-testing.md](./09-testing.md) | テスト（pytest・unittest） |
| **第10章** | [10-package-structure.md](./10-package-structure.md) | パッケージ構造と import |
| **第11章** | [11-logging-docstring.md](./11-logging-docstring.md) | ロギングと docstring |
| **第12章** | [12-exception-design.md](./12-exception-design.md) | 例外の設計 |
| **第14章** | [14-classes-oop.md](./14-classes-oop.md) | クラスとオブジェクト指向 |
| **第15章** | [15-env-variables.md](./15-env-variables.md) | 環境変数 |
| **第16章** | [16-decorators.md](./16-decorators.md) | デコレータ |
| **第17章** | [17-json-serialization.md](./17-json-serialization.md) | JSON の読み書き |
| **第18章** | [18-dataclass.md](./18-dataclass.md) | dataclass |
| **第19章** | [19-pathlib.md](./19-pathlib.md) | pathlib（パス操作） |
| **第20章** | [20-regex.md](./20-regex.md) | 正規表現（re） |
| **第21章** | [21-builtin-functions.md](./21-builtin-functions.md) | 組み込み関数（map, filter, zip） |
| **用語集** | [22-glossary.md](./22-glossary.md) | 用語集 |

---

## 読み方のすすめ

- **順番に読む**と、基本 → データ型 → 関数 → まとめ、と流れで理解しやすくなります。
- 各章の**例題**には、**各行にコメント**を付けているので、ソースコードを追いながら「何をしているか」を確認できます。
- 用語が分からないときは、その章の冒頭や「まとめ」を見直すと、要点が整理されています。

---

## 前提

- **プログラミング未経験**の方でも、1章から順に読めば理解できるように書いています。
- 実際に Python を動かすには、**Python** をインストールし、ターミナル（コマンドライン）またはエディタで実行します。

---

## フォルダ構成

```
Python/
├── README.md
├── 01-basics.md ～ 21-builtin-functions.md
├── 22-glossary.md（用語集・最終章）
├── frameworks/           ← Web フレームワーク
│   ├── README.md
│   ├── flask/
│   ├── django/
│   ├── fastapi/
│   └── bottle/
└── libraries/            ← よく使うライブラリ
    ├── README.md
    ├── requests/
    ├── pytest/
    ├── pandas/
    ├── sqlalchemy/
    └── pygame/
```

各章の MD ファイルを開いて、順に読み進めてください。
