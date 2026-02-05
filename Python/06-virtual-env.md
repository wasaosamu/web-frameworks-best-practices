# 第6章：仮想環境と requirements.txt

**この章の目標**  
**仮想環境**（venv）と **requirements.txt** の使い方を学びます。  
プロジェクトごとにパッケージを分けて管理する方法を、初心者向けに説明します。

---

## 仮想環境とは？

**仮想環境**は、**プロジェクトごとにパッケージを分けて入れておく仕組み**です。

- プロジェクトAでは Flask 1.0、プロジェクトBでは Flask 2.0、のように**バージョンを分けられる**  
- システムの Python を汚さずに済む  
- ほぼすべての Python プロジェクトで使われる  

---

## 仮想環境の作り方（venv）

Python には **venv** が標準で入っています。

```bash
# 仮想環境を作る（myproject フォルダ内で実行する想定）
python -m venv venv

# 有効化（Windows）
venv\Scripts\activate

# 有効化（macOS / Linux）
source venv/bin/activate
```

- **venv** は仮想環境用のフォルダ名（任意。`env` や `.venv` でもよい）  
- 有効化すると、ターミナルの先頭に `(venv)` が付く  

---

## 仮想環境の無効化

```bash
deactivate
```

---

## requirements.txt とは？

**requirements.txt** は、**このプロジェクトで使うパッケージ一覧**を書いたファイルです。

- 他の人が `pip install -r requirements.txt` で同じ環境を再現できる  
- バージョンも書いておくと、再現性が高まる  

---

## requirements.txt の書き方

```
flask==3.0.0
requests>=2.28.0
```

| 書き方 | 意味 |
|--------|------|
| `flask==3.0.0` | バージョン 3.0.0 だけを使う |
| `requests>=2.28.0` | 2.28.0 以上なら OK |

---

## インストール済みパッケージを書き出す

```bash
pip freeze > requirements.txt
```

- 今入っているパッケージとバージョンが、`requirements.txt` に書き出される  
- 手動で不要なものを削ってからコミットするとよい  

---

## まとめ

- **仮想環境**（venv）で、プロジェクトごとにパッケージを分ける。
- **requirements.txt** で、使うパッケージ一覧を共有・再現する。
- 新しいプロジェクトでは、まず venv を作り、`pip install` したら `pip freeze > requirements.txt` する癖をつけるとよい。
