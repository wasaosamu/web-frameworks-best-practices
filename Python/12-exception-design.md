# 第12章：例外の設計

**この章の目標**  
**例外**をどう扱うか、設計の考え方を学びます。  
いつ try/except を使うか、独自の例外をどう作るかを、初心者向けに説明します。

---

## 例外とは？

**例外**は、**想定外の事態が起きたとき**に発生するものです。

- ファイルが存在しない、ネットワークエラー、ゼロ除算など  
- 例外をそのままにすると、プログラムが止まってしまう  
- **try / except** で捕まえて、適切に処理する  

---

## 基本的な try / except

```python
try:
    number = int(user_input)
except ValueError:
    print("数値を入力してください")
```

- **ValueError** は、`int()` に数値に変換できない文字列を渡したときに発生  
- except で捕まえて、ユーザーに分かりやすいメッセージを出す  

---

## 複数の例外を扱う

```python
try:
    with open("file.txt") as f:
        content = f.read()
except FileNotFoundError:
    print("ファイルが見つかりません")
except PermissionError:
    print("読み取りの権限がありません")
```

---

## 例外を再送出する

「ここでは処理せず、呼び出し元に任せる」場合があります。

```python
def load_config(path):
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError:
        # ログを出してから、同じ例外を再送出
        logging.error(f"設定ファイルが見つかりません: {path}")
        raise
```

- **raise** だけ書くと、捕まえた例外をそのまま再送出する  

---

## 独自の例外を定義する

```python
class UserNotFoundError(Exception):
    """ユーザーが見つからないときの例外"""
    pass

def get_user(id: int):
    user = find_in_db(id)
    if user is None:
        raise UserNotFoundError(f"ユーザー ID {id} は存在しません")
    return user
```

- **Exception** を継承したクラスを定義する  
- 意味の分かる名前を付けると、何が起きたか分かりやすくなる  

---

## まとめ

- **try / except** で例外を捕まえ、ユーザー向けメッセージやログを出す。
- 複数種類の例外は、それぞれ **except** で分けて処理する。
- 処理しない場合は **raise** で再送出する。
- **独自の例外**は Exception を継承して定義し、意味の分かる名前を付ける。
