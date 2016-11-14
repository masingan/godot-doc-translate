## プロジェクト編成

### イントロダクション

このチュートリアルではプロジェクト編成に関する簡単なワークフローを提案します。エンジンを最初に使う時、Godotではファイルシステムがプロジェクト編成の基本となっています。使い始めはこのシステムに違和感があるかもしれないので、このチュートリアルが存在しているわけです。

このチュートリアルでは、バージョンコントロールシステムをGodotに応用することについても説明して行きます。

### 編成

Godot以外のゲームエンジンにはアセット用のデータベースがあったりします。画像やモデルやサウンドなどには、それぞれのブラウザを通じてアクセスします。Godotは基本的にシーンという単位がベースなので、アセットはシーンに紐付けられ、シーンから参照されます。

### インポートとゲームフォルダー

Godotでは、アセットをインポートする必要があります。インポートするアセットのオリジナルソースは、そのままGodotのリソースとしても認識できてしまいます。このような仕様から、双方が同じプロジェクトフォルダ内にある場合には、アセットが二重で参照されてしまうというバグが起こります。

そのような背景から、Godotでゲームを作る際は、アセットのフォルダとソースコードのフォルダを分けることを推奨します。これにより、svnやgitなどのバージョンコントロールシステムを使う際の親和性も向上します。例えば、プロジェクトフォルダ内に目的ごとのディレクトリを作成して:

```
myproject/art/models/house.max
myproject/art/models/sometexture.png
myproject/sound/door_open.wav
myproject/sound/door_close.wav
myproject/translations/sheet.csv
```

ゲームのソースコードなどはgame/に置きます。

```
myproject/game/engine.cfg
myproject/game/scenes/house/house.scn
myproject/game/scenes/house/sometexture.tex
myproject/game/sound/door_open.smp
myproject/game/sound/door_close.smp
myproject/game/translations/sheet.en.xl
myproject/game/translations/sheet.es.xl
```

この構成を採用すると、以下のように問題が解決されることになります。

* プロジェクト全体がプロジェクトフォルダ内にあり管理がしやすい。

* Godotはgame/以下を参照するため、アセットのオリジナルはエクスポートされない。

* myproject/以下を直接バージョンコントロールシステムに突っ込むことが出来る。

* Godotは相対パスでアセットを読み込むため、myproject/以下で完結しており、チームでの作業の際のポータビリティが確保出来る。

### シーンの編成

ゲームフォルダ内ではどのようにシーンを編成していけばいいのでしょうか。デベロッパの多くはアセットのタイプをベースにして編成を試みるでしょうが、この方法では混乱が生じます。最適な編成方法はアセットのタイプではなく、ゲームの機能ごとにディレクトリを切ることです。

例えば、アセットのタイプごとにシーンを編成する場合は次のようになります。

```
game/engine.cfg
game/scenes/scene1.scn
game/scenes/scene2.scn
game/textures/texturea.png
game/textures/another.tex
game/sounds/sound1.smp
game/sounds/sound2.wav
game/music/music1.ogg
```

この方法は一見良さそうにも見えますが、実は最悪です。この方法で分けたプログラムを一度管理してみれば分かりますが、プロジェクトが大きくなっていく毎に管理が出来なくなります。

ゲームの内容でディレクトリを切ると以下のようになります。こちらをオススメします。

```
game/engine.cfg
game/scenes/house/house.scn
game/scenes/house/texture.tex
game/scenes/valley/canyon.scn
game/scenes/valley/rock.scn
game/scenes/valley/rock.tex
game/scenes/common/tree.scn
game/scenes/common/tree.tex
game/player/player.scn
game/player/player.gd
game/npc/theking.scn
game/npc/theking.gd
game/gui/main_screen/main_sceen.scn
game/gui/options/options.scn
```

この方法であれば、ゲームのプロジェクトのスケールがどれだけ大きくなっても適切に管理することが出来ます。例えばgame/scenes/valley/には谷の場面のシーンファイルのソースコードが入っていることが分かりますね。このように命名規則を適切に作っておくことで、そのファイルが何であるのかが分かりやすくなり、管理コストが大幅に低減します。特にGodotでは名前を付けられるファイルは全てどこかのシーンに属すはずなのでこのような管理手法が有効に機能します。

### ファイルのキャッシュ

Godotではプロジェクトのルートに.fscasheと言う隠しファイルが作られます。プロジェクトファイルをキャッシュし、いつ変更されたかを記録するためのファイルです。このファイル内には自分のマシンのローカルの情報が含まれていますので、gitなどを使用する際には、このファイルをコミットしないようにしてください。
