# pygame：イベントと描画

**この章の目標**  
**キー入力**と**図形の描画**の基本を学びます。  
キャラクターを動かしたり、簡単な図形を描くための基礎です。

---

## キー入力の取得

```python
for event in pygame.event.get():
    if event.type == pygame.KEYDOWN:     # キーが押された瞬間のイベント
        if event.key == pygame.K_LEFT:   # 左矢印キー
            x -= 5                        # x 座標を左に移動
        if event.key == pygame.K_RIGHT:  # 右矢印キー
            x += 5                        # x 座標を右に移動
```

- **KEYDOWN** でキーが押されたイベントを検知  
- **event.key** でどのキーか判定（**K_LEFT**、**K_RIGHT**、**K_SPACE** など）  

---

## 押しっぱなしに対応する

```python
keys = pygame.key.get_pressed()   # 現在押されているキーを取得
if keys[pygame.K_LEFT]:
    x -= 5
if keys[pygame.K_RIGHT]:
    x += 5
```

- **get_pressed()** で「今押されているか」を毎フレーム確認  
- 押しっぱなしで連続して動かせる  

---

## 図形の描画

```python
screen.fill((0, 0, 0))                    # 画面を黒で塗りつぶす（R, G, B は 0〜255）

pygame.draw.rect(screen, (255, 0, 0), (x, y, 50, 50))   # 四角形：赤、(x,y) から幅50高さ50
pygame.draw.circle(screen, (0, 255, 0), (100, 100), 30) # 円：緑、中心(100,100)、半径30
pygame.draw.line(screen, (255, 255, 0), (0, 0), (100, 100), 3)  # 線：黄、(0,0) から (100,100)、太さ3
```

- **fill((R, G, B))** で背景色を指定（0〜255）  
- **draw.rect(画面, 色, (x, y, 幅, 高さ))** で四角形  
- **draw.circle(画面, 色, 中心座標, 半径)** で円  

---

## テキストの表示

```python
font = pygame.font.Font(None, 36)              # フォントを作成（None でデフォルト、36 はサイズ）
text = font.render("Hello, pygame!", True, (255, 255, 255))  # テキストを Surface に変換（白）
screen.blit(text, (100, 100))                  # 画面の (100, 100) に貼り付けて表示
```

- **font.render(文字列, アンチエイリアス, 色)** でテキストを Surface に変換  
- **blit(Surface, 座標)** で画面に貼り付ける  

---

## まとめ

- **KEYDOWN** や **get_pressed()** でキー入力を取得する。
- **draw.rect()**、**draw.circle()** で図形を描画する。
- **font.render()** と **blit()** でテキストを表示する。
