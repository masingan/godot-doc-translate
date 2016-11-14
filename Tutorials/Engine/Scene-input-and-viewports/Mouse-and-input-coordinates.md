## マウスとインプット座標

### About

このチュートリアルは、インプット座標やマウスの位置取得、画面解像度などに関する、小さいながらありがちなミスを無くすためのものです。

### ハードウエアディスプレイ座標

ハードウエア座標は、複合UIを書く時、つまりPC上でエディターやMMO、ツールなどを動かす時に使うことが出来ます。そのスコープを出てしまったら参照することは出来ません。

### ビューポートディスプレイ座標

Godotはコンテンツを表示するためにビューポートを使います、ビューポートは様々なオプションをもとに縮小拡大されます(複数解像度のチュートリアルを見てください)。それから、マウス座標や、ビューポートサイズの取得のための関数を使ってください。以下は例です。

```python

func _input(ev):
   # ビューポート内のマウスの座標を取得

   if (ev.type==InputEvent.MOUSE_BUTTON):
       print("Mouse Click/Unclick at: ",ev.pos)
   elif (ev.type==InputEvent.MOUSE_MOTION):
       print("Mouse Motion at: ",ev.pos)

   # ビューポートのサイズの表示

   print("Viewport Resolution is: ",get_viewport_rect().size)

func _ready():
    set_process_input(true)

```

または、マウスの位置をビューポートに問い合わせることも可能です。

```python
get_viewport().get_mouse_pos()
```
