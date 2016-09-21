## スクリプティング(続き)

### プロセッシング

Godotではいくつかのアクションについてコールバックや、仮想関数がトリガーとなるので、専用のコードを書いて全ての場合に正常に動作するかといったチェックは不要です。更に言えば、ほとんどの動作チェックはアニメーションプレイヤーで完結します。

しかし、フレーム毎に特定のスクリプトを実効させたいというケースもありますね。プロセッシングには2つのタイプがあります。アイドルプロセッシングとフィクシードプロセッシングです。

アイドルプロセッシングはNode.set_prosess()関数によってアクティブになります。一度アクティブになればNode._prosess()コールバックがフレーム毎に呼ばれます。

例

```python
func _ready():
	set_process(true)
	
func _process(delta):
	# do something...
```

deltaという値には_proces()が呼ばれてからの経過時間が入ります。これは浮動小数の秒で表されます。フィクシードプロセッシングはこれと似ていますが、物理エンジンとの同期が必要である点が唯一の違いと言えます。

簡単なテストをしましょう。シーンを作成し、ラベルノードを作成し、以下のコードを紐付けましょう。

```python
extend Label

var accum=0

func _readu():
	set_process(true)
	
func _process(delta):
	accum += delta
	set_text(str(accum))

```

値がどんどん大きくなれば成功です。

## グループ

ノードはそれぞれのノードについて好きな数のグループに入れることができます。単純なことですが、大きなプロジェクトになるほど使える機能です。グループに追加する方法は二種類あって、GUIのグループボタンを押す方法。

![groups](./Learning-step-by-step-assets/Scripting-continued/groups.png)

それと、コードから追加する方法です。

```python
func _ready():
	add_to_group("enemies")
```

いかに便利であるかという例を示したいと思います。敵のタグ付けの例です。この例では、プレイヤーは敵の秘密基地を探索していて、見つかったと言うシチュエーションを想定します。敵は全員気づき、アラームが鳴らされます。この関数をSceneTree.call_group()で実現します。

```python
func _on_discovered():
	get_tree().call_group(0,"guards","player_was_discovered")
```

上のコードは”guards”グループの全てのメンバーに”player_was_discovered”関数を呼ぶものです。

また、SceneTree.get_node_in_group()を使えば全ての"guards"グループのメンバーのリストを取得することもできます。

```python
var guards = get_tree().get_nodes_in_group("guards")
```

SceneTreeについてはのちほど詳しく解説します。

## 通知

Gogotには通知システムも内蔵されています。これは普通はわざわざスクリプトで使用することは無いです。とても低レベルなものなので殆どの場合仮想関数が用意されています。この機能も一応テストしてみましょう。簡単なObject._notification()関数をスクリプトに追加しましょう。

```python
func _notification(what)
	if (what == NOTIFICATION_READY):
		print("This is the same as overriding _ready()...")
	elif (what == NOTIFICATION_PROCESS):
		var delta = get_process_time()
		print("This is the same as overriding _proccess()...")
```

Class Referenceにある各クラスのドキュメントには通知は受け取ることも可能である旨が書かれています。もう一度言いますが殆どのケースでもっと簡単なオーバーライドの出来る関数が用意されています。

## オーバーライド可能な関数

既に話題に上がりましたが、オーバーライド可能な関数は使った方が良いです。ノードには沢山の使えるオーバーライド可能な関数が用意されていています。例えば以下のようなものがあります。

```python
func _enter_tree():
    # Scene Treeに入った時にアクティブになりこの関数
    # が呼ばれる。 呼ばれた時点で、子ノードはまだアクティブな
    # シーンには入っていない。 一般的には_ready()関数を
    # 呼んだほうが良い。
    pass

func _ready():
    # _enter_tree()の後にこの関数が呼ばれるが、これは全ての
    # 子ノードを含めシーンツリーに入りアクティブになった
    # を保証する。
    pass

func _exit_tree():
    # ノードがScene Treeから出た時、この関数は呼ばれる。
    # 呼ばれた瞬間で、子ノードはScene Treeを出ており無効化
    # されている。
    pass

func _process(delta):
    # set_process()が有効である時、毎フレーム呼ばれる。
    pass

func _fixed_process(delta):
    # set_fixed_process()が有効である時、物理判定の度に
    # 呼ばれる。
    pass

func _paused():
    # ゲームがポーズされた時に呼ばれる。 これが呼ばれた後
    # ノードはどんなコールバックも受け付けなくなる
    pass

func _unpaused():
    # ゲームのポーズが解除された時に呼ばれる。
    pass
```

## ノードを作成する

コードからノードを作成します。.new()メソッドを使うだけで実現出来ます。他のクラスベースのデータタイプと同じですね。例えば

```python
var s
func _ready():
	s = Sprite.new() # create a new sprite!
	add_child(s) # このノードの子ノードとして追加
```

シーンの切り替わりの際にノードを消去するには、free()関数が使われます。

```python
func _someaction():
	s.free() # ノードを削除し、領域を開放する
```

ノードが開放された時、その子ノードも開放されます。このため、ノードを用意するより削除する方がとても単純です。木構造のベースになっているノードを開放すれば、その先の木構造は全て開放されます。

しかし、現在"blocked"であるノードというのがままあります。"blocked"はシグナルを発してるか、何か関数を呼んでいる時に起こります。

"blocked"なノードを開放しようとすればゲームはクラッシュしてしまいます。Godotのデバッガーはこのケースを捉えると警告を出します。

より安全にノードを消す関数が用意されています。Node.queue_free()です。これはノードがアイドル状態の時に削除を行うのでとても安全です。

```python
func _someaction():
	s.queue_free() # ノードに何も起こっていなければ削除する
```

## シーンのインスタンス化

コードからシーンをインスタンス化するのはとても簡単で、2ステップで完了します。まず、シーンをディスクから読み込みます。

```python
var scene = load("res://myscene.scn") # スクリプトがインスタンス化された時にロードする
```

プレロードの方が便利な場合があります。これはパース時に実行されます。

```python
var scene = preload("res://myscene.scn") # スクリプトのパース中にロードする
```

シーンはまだサブノードを格納したノードにはなっていません。それはPackedSceneと呼ばれる特別なリソースに格納されています。実際のノードを作るにはPackedScene.instance()関数を呼ぶ必要があります。これはアクティブなシーンに加えることが出来るノードの木構造を返します。

```python
var node = scene.instance()
add_child(node)
```

このツーステップのアドバンテージはパッケージ化されたシーンがロードされつつ次に別のものとして使う準備ができているということです。つまり望むだけインスタンスを配置出来ます。アクティブなシーンの中で沢山の敵や弾丸を配置する際にとても役に立ちます。
