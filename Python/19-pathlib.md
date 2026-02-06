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
from pathlib import Path

# 現在のディレクトリ
p = Path(".")
# または絶対パス
p = Path("/Users/username/project")

# ファイルのパス
config_path = Path("config") / "settings.json"
# Path 同士を / でつなげられる
```

---

## よく使うメソッド

```python
from pathlib import Path

p = Path("data/config.json")

# 存在するか
p.exists()       # True / False

# ファイルか、フォルダか
p.is_file()      # ファイルなら True
p.is_dir()       # フォルダなら True

# パスの各部分
p.name           # "config.json"
p.stem           # "config"（拡張子なし）
p.suffix         # ".json"
p.parent         # "data" のパス

# 絶対パスに変換
p.resolve()
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

folder = Path("data")

# 直下のファイル・フォルダ
for item in folder.iterdir():
    print(item.name)

# 再帰的にすべて列挙
for item in folder.rglob("*.py"):
    print(item)
```

---

## まとめ

- **pathlib.Path** でパスをオブジェクトとして扱う。
- **/ で連結**、**exists()**、**is_file()**、**read_text()** などがよく使える。
- `os.path` より直感的で、実務では pathlib を使うのがおすすめ。
