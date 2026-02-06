# pygame 入門

**この章の目標**  
**pygame** を使って、ゲームやグラフィックアプリの基本を学びます。  
ウィンドウ表示とゲームループの考え方を、初心者向けに説明します。

---

## pygame とは？

**pygame** は、**ゲーム開発**のためのライブラリです。

- ウィンドウ表示、描画、キー入力、音声などを扱える  
- 2D ゲームのプロトタイプや学習に向いている  
- 「ライブラリ」なので、ゲームループなどは自分で組み立てる  

---

## インストール

```bash
pip install pygame          # pygame を pip でインストール
```

---

## 最小限のプログラム（ウィンドウ表示）

```python
import pygame               # pygame をインポート

pygame.init()               # pygame を初期化（必須・最初に呼ぶ）

screen = pygame.display.set_mode((640, 480))  # 640x480 のウィンドウを作成
pygame.display.set_caption("My Game")         # ウィンドウのタイトルを設定

running = True
while running:              # ゲームループ（1フレームごとに繰り返す）
    for event in pygame.event.get():  # イベント（キー、マウスなど）を取得
        if event.type == pygame.QUIT:  # 閉じるボタンが押されたら
            running = False             # ループを抜ける

    pygame.display.flip()   # 描画を画面に反映する

pygame.quit()               # pygame を終了
```

- **pygame.init()** で初期化、**pygame.quit()** で終了  
- **set_mode(幅, 高さ)** でウィンドウを作成  
- **ゲームループ**の中で、イベント処理と描画を繰り返す  

---

## ゲームループとは？

**ゲームループ**は、**毎フレームごとに実行される処理**の繰り返しです。

1. イベントを取得（キー入力、マウス、閉じるボタンなど）  
2. ゲームの状態を更新  
3. 画面を描画  
4. 画面を更新（flip）  

---

## まとめ

- **pygame.init()** で初期化し、**set_mode()** でウィンドウを作成する。
- **ゲームループ**で **event.get()** と **display.flip()** を繰り返す。
- **QUIT** イベントでループを抜け、**pygame.quit()** で終了する。
