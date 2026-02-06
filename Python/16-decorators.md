# 第16章：デコレータ

**この章の目標**  
**デコレータ**の基本を学びます。  
`@` で関数をラップする仕組みを、初心者向けに説明します。

---

## デコレータとは？

**デコレータ**は、**関数の前後に処理を追加する**ための仕組みです。

- `@` で始まる記法で関数の上に書く  
- Flask の `@app.route("/")` や、ログ出力、タイム計測などで使う  

---

## 既存のデコレータの例

```python
# Flask の例
@app.route("/")         # デコレータ：この関数をルート URL "/" に紐付ける
def index():
    return "Hello"      # リクエストがあったときに返すレスポンス

# @app.route("/") は「この関数を / の URL に紐付ける」という意味
```

---

## デコレータの自作

```python
def log_calls(func):            # 関数を受け取るデコレータ関数
    def wrapper(*args, **kwargs):  # 元の関数を包む内部関数（任意の引数を受け取る）
        print(f"呼び出し: {func.__name__}")  # 関数名を表示してから
        return func(*args, **kwargs)          # 元の関数を実行し、戻り値をそのまま返す
    return wrapper              # ラップした関数を返す

@log_calls                      # greet = log_calls(greet) と同じ
def greet(name):
    return f"こんにちは、{name} さん"

greet("山田")                   # 呼び出し: greet と表示されてから、挨拶が返る
```

- **log_calls** は「関数を受け取り、新しい関数を返す」関数  
- **@log_calls** は `greet = log_calls(greet)` と同じ意味  

---

## 引数を受け取るデコレータ

```python
def repeat(times):              # デコレータの引数（繰り返し回数）を受け取る
    def decorator(func):        # 実際のデコレータ（関数を受け取る）
        def wrapper(*args, **kwargs):  # 元の関数を包む内部関数
            for _ in range(times):     # times 回ループ
                func(*args, **kwargs)  # 元の関数を実行
        return wrapper
    return decorator

@repeat(3)                      # say_hello を3回実行するデコレータを適用
def say_hello():
    print("Hello")

say_hello()                     # Hello が3回表示される
```

- デコレータ自体が引数を受け取る場合は、**2段の関数**になる  

---

## まとめ

- **デコレータ**は関数をラップし、前後に処理を追加する仕組み。
- `@名前` で関数にデコレータを適用する。
- 自作するときは「関数を受け取り、新しい関数を返す」形にする。
