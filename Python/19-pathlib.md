# 第19章：pathlib（パス操作）

**この章の目標**  
**pathlib** を使って、ファイルやフォルダのパスを扱う方法を学びます。  
実務でよく使う、OS に依存しにくいパス操作を、初心者向けに説明します。

---

## pathlib とは？

**pathlib** は、**ファイルパスをオブジェクトとして扱う**ための標準ライブラリです。

- `os.path` よりも直感的で、メソッドチェーンが書ける  
- Windows と macOS/Linux の違いを吸収してくれる  
- Python 3.4 以降で標準搭載  

---

## Path の基本

```python
from pathlib import Path           # pathlib から Path をインポート

# 現在のディレクトリ
p = Path(".")                      # カレントディレクトリを表す Path
# または絶対パス
p = Path("/Users/username/project")  # 絶対パスで Path を作成

# ファイルのパス
config_path = Path("config") / "settings.json"  # / でパスを連結（OS に依存しない）
```

---

## よく使うメソッド

```python
from pathlib import Path

p = Path("data/config.json")       # 相対パスで Path を作成

# 存在するか
p.exists()       # パスが存在するか True/False で返す

# ファイルか、フォルダか
p.is_file()      # ファイルなら True
p.is_dir()       # ディレクトリなら True

# パスの各部分（属性で取得）
p.name           # ファイル名（拡張子含む）→ "config.json"
p.stem           # ファイル名（拡張子なし）→ "config"
p.suffix         # 拡張子 → ".json"
p.parent         # 親ディレクトリの Path → "data"

# 絶対パスに変換
p.resolve()      # 絶対パスに解決した Path を返す
```

---

## ファイルの読み書き

```python
from pathlib import Path

p = Path("data.txt")

# 読み込み
content = p.read_text(encoding="utf-8")

# 書き込み
p.write_text("Hello", encoding="utf-8")

# バイナリで読み書き
data = p.read_bytes()
p.write_bytes(data)
```

- **read_text** / **write_text** でテキストを扱う  
- **encoding="utf-8"** で日本語にも対応  

---

## フォルダ内のファイルを列挙する

```python
from pathlib import Path

folder = Path("data")              # 対象フォルダの Path

# 直下のファイル・フォルダ
for item in folder.iterdir():      # 直下のエントリを1つずつ取得
    print(item.name)               # 各エントリの名前を出力

# 再帰的にすべて列挙
for item in folder.rglob("*.py"):  # サブフォルダ含め *.py に一致するパスを検索
    print(item)                    # 一致した Path を出力
```

---

## まとめ

- **pathlib.Path** でパスをオブジェクトとして扱う。
- **/ で連結**、**exists()**、**is_file()**、**read_text()** などがよく使える。
- `os.path` より直感的で、実務では pathlib を使うのがおすすめ。
