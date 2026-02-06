# pytest：フィクスチャ

**この章の目標**  
**フィクスチャ**を使って、テストの前処理を共通化する方法を学びます。  
DB のセットアップやモックの準備によく使います。

---

## フィクスチャとは？

**フィクスチャ**は、**テストの前に行う準備処理**をまとめたものです。

- `@pytest.fixture` で関数を登録する  
- テスト関数の引数にその名前を書くと、自動で実行されて結果が渡される  
- 複数のテストで同じ準備を使い回せる  

---

## 基本的な使い方

```python
import pytest

@pytest.fixture
def sample_data():            # フィクスチャを定義
    return [1, 2, 3]          # テストに渡すデータを返す

def test_sum(sample_data):    # 引数にフィクスチャ名を書くと、戻り値が渡される
    assert sum(sample_data) == 6
```

- **@pytest.fixture** を付けた関数がフィクスチャになる  
- テスト関数の引数名とフィクスチャ名を一致させる  

---

## セットアップとクリーンアップ

```python
@pytest.fixture
def temp_file(tmp_path):      # tmp_path は pytest が提供する一時ディレクトリ
    file_path = tmp_path / "test.txt"
    file_path.write_text("hello")
    yield file_path           # テストに file_path を渡す
    # yield の後はクリーンアップ（必要ならここに書く）
```

- **yield** を使うと、テスト後にクリーンアップを書ける  
- **tmp_path** は pytest 標準の一時ディレクトリフィクスチャ  

---

## まとめ

- **@pytest.fixture** でフィクスチャを定義する。
- テスト関数の引数にフィクスチャ名を書くと、戻り値が渡される。
- **yield** でセットアップとクリーンアップを分けられる。
