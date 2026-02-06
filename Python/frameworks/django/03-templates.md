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
from django.shortcuts import render

def index(request):
    context = {
        "name": "山田",
        "items": ["りんご", "みかん", "ばなな"]
    }
    return render(request, "index.html", context)
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
<!-- base.html -->
<html>
<head><title>{% block title %}サイト{% endblock %}</title></head>
<body>
  {% block content %}
  {% endblock %}
</body>
</html>

<!-- index.html -->
{% extends "base.html" %}
{% block content %}
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
