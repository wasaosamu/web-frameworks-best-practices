# Flask：テンプレート（Jinja2）

**この章の目標**  
**Jinja2** テンプレートを使って、HTML を動的に生成する方法を学びます。  
第1章で学んだルーティングに続く、実務でよく使う機能です。

---

## テンプレートとは？

**テンプレート**は、**変数などを埋め込んだ HTML** です。

- 同じ構造の HTML で、中身だけ変えて表示したいときに使う  
- Flask は **Jinja2** を標準で使う  
- `templates/` フォルダに .html ファイルを置く  

---

## フォルダ構成

```
myapp/
├── app.py
└── templates/
    └── index.html
```

---

## テンプレートの描画

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html", name="山田")
```

- **render_template("ファイル名", 変数名=値)** でテンプレートを描画する  
- 変数はテンプレート内で `{{ 変数名 }}` で使える  

---

## テンプレート内での変数表示

```html
<!-- templates/index.html -->
<!DOCTYPE html>
<html>
<head><title>Flask アプリ</title></head>
<body>
  <h1>こんにちは、{{ name }} さん</h1>
</body>
</html>
```

- **{{ 変数名 }}** で変数の値を出力する  

---

## 条件分岐とループ

```html
{% if name %}
  <p>こんにちは、{{ name }} さん</p>
{% else %}
  <p>こんにちは、ゲストさん</p>
{% endif %}

<ul>
{% for item in items %}
  <li>{{ item }}</li>
{% endfor %}
</ul>
```

- **{% if %}** / **{% else %}** / **{% endif %}** で条件分岐  
- **{% for %}** / **{% endfor %}** でループ  

---

## まとめ

- **render_template()** でテンプレートを描画する。
- **{{ 変数 }}** で値を出力、**{% if %}** / **{% for %}** で制御する。
- テンプレートは `templates/` フォルダに置く。
