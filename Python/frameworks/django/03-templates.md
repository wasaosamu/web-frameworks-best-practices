# Django：テンプレート

**この章の目標**  
**Django テンプレート**を使って、HTML を動的に生成する方法を学びます。  
変数、ループ、継承の基本を、初心者向けに説明します。

---

## テンプレートとは？

**テンプレート**は、**変数やロジックを埋め込んだ HTML** です。

- Django は独自のテンプレート言語を使う  
- 各アプリの `templates/` フォルダに .html を置く  
- ビューから `render()` でテンプレートに変数を渡す  

---

## ビューからテンプレートを描画

```python
# myapp/views.py
from django.shortcuts import render   # テンプレート描画用の render をインポート

def index(request):
    context = {                       # テンプレートに渡す変数の辞書
        "name": "山田",
        "items": ["りんご", "みかん", "ばなな"]
    }
    return render(request, "index.html", context)  # テンプレートに context を渡して描画
```

- **render(request, "テンプレート名", 辞書)** でテンプレートを描画する  
- 辞書のキーがテンプレート内の変数名になる  

---

## テンプレート内での変数とループ

```html
<!-- myapp/templates/index.html -->
<h1>こんにちは、{{ name }} さん</h1>

<ul>
{% for item in items %}
  <li>{{ item }}</li>
{% endfor %}
</ul>

{% if name %}
  <p>{{ name }} が表示されています</p>
{% endif %}
```

- **{{ 変数 }}** で値を出力  
- **{% for %}** / **{% endfor %}** でループ  
- **{% if %}** / **{% endif %}** で条件分岐  

---

## テンプレートの継承

```html
<!-- base.html（ベーステンプレート） -->
<html>
<head><title>{% block title %}サイト{% endblock %}</title></head>  <!-- block で上書き可能な部分を定義 -->
<body>
  {% block content %}
  {% endblock %}
</body>
</html>

<!-- index.html（子テンプレート） -->
{% extends "base.html" %}           <!-- ベーステンプレートを継承 -->
{% block content %}                 <!-- content ブロックを上書き -->
  <h1>メインの内容</h1>
{% endblock %}
```

- **{% extends %}** でベーステンプレートを指定  
- **{% block %}** で上書きする部分を定義する  

---

## まとめ

- **render()** でテンプレートに変数を渡して描画する。
- **{{ 変数 }}**、**{% for %}**、**{% if %}** を使う。
- **{% extends %}** と **{% block %}** で共通レイアウトを継承できる。
