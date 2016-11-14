## ゲームのセーブ

### イントロダクション

ゲームのセーブ処理は複雑化する可能性が十分にあります。現在のレベルやステージで獲得した星の数などよりも多くの情報を格納した方が好ましい場合があるからです。そのような高度なセーブ処理を必要とするゲームでは、様々なオブジェクトに関する情報を追加で格納する必要があるのです。ゲームが複雑になるにつれ、セーブ処理は複雑化していく傾向があると言えます。

### 保持するオブジェクトを識別

まず、そのゲームではどのようなオブジェクトの情報をキープしたいのか、そのオブジェクトのどんな情報をセーブしていくべきかを吟味する必要があります。このチュートリアルでは、グループ化を使用してセーブするオブジェクトをマークし処理しますが、セーブのロジックはこの方法だけでは無いです。

"Persisit"グループに保存したいオブジェクトを追加することから始めます。スクリプティング(続き)チュートリアルで解説したように、GUIまたはスクリプトを用いて追加可能です。まずはGUIでノードを追加しましょう。

![groups](./Filesystem-assets/Saving-games/groups.png)

ゲームをセーブする際には、全てのオブジェクトに対してセーブを命令します。

```python
var savenodes = get_tree().get_nodes_in_group("Persist")
    for i in savenodes:
    # 全てのノードに対してセーブ関数を呼ぶ.
```

### シリアル化

次にデータをシリアル化します。シリアル化とは読み込みやセーブなどディスクとのやりとりの最適化です。Persisitグループはインスタンス化されたノードであり、パスがあると仮定します。GDScriptにはDictionary.to_json()やDictionary.parse_json()など、便利な関数が多数あるので、今回はディクショナリーを使うことにしましょう。セーブ用のノードにはセーブ関数とセーブするデータが必要です。セーブ関数は以下のようになります。:

```python
func save():
    var savedict = {
        filename=get_filename(),
        parent=get_parent().get_path(),
        posx=get_pos().x, #Vector2 is not supported by json
        posy=get_pos().y,
        attack=attack,
        defense=defense,
        currenthealth=currenthealth,
        maxhealth=maxhealth,
        damage=damage,
        regen=regen,
        experience=experience,
        TNL=TNL,
        level=level,
        AttackGrowth=AttackGrowth,
        DefenseGrowth=DefenseGrowth,
        HealthGrowth=HealthGrowth,
        isalive=isalive,
        last_attack=last_attack
    }
    return savedict
```

この方法ではディクショナリーは連想配列になります。 ```{ "variable_name":that_variables_value }``` 連想配列はロード時に最適な形です。


### セーブとデータの読み込み

ファイルシステムのチュートリアルでも述べましたが、ファイルからデータを読み込むには、まずファイルをオープンして書き込むする必要があります。グループに関連するデータを呼び出すメソッドがあります。グループを呼び出し、to_json()を使って保存に適した文字列に変換し、ファイルに格納しましょう。この方法では、各行が確実に独自のjsonオブジェクトになり、ファイルからデータを取り出しやすくなります。

```python
# ノート:この関数はツリーのどこからでも呼び出せます。パスには依存しません。
# Persistカテゴリの全てに対して、関連する変数のdictを返すよう指示します。
func save_game():
    var savegame = File.new()
    savegame.open("user://savegame.save", File.WRITE)
    var savenodes = get_tree().get_nodes_in_group("Persist")
    for i in savenodes:
        var nodedata = i.save()
        savegame.store_line(nodedata.to_json())
    savegame.close()
```

ゲームのセーブについてはこれで終わりです。このセーブ方法であれば読み込むことも容易です。各行を読み込んでparse_json()で値を読み込み、その値をオブジェクトに代入すれば読み込み処理は出来てしまいます。だたし、オブジェクトを作成する前に、少々面倒な作業が必要になります。ファイル名や親の値を使用出来るかのチェックです。例えばこのようなロード関数が考えられます。

```python
# ノート:この関数はツリーのどこからでも呼び出せます。パスには依存しません。
func load_game():
    var savegame = File.new()
    if !savegame.file_exists("user://savegame.save"):
        return #Error!  We don't have a save to load

    # ゲームステートを初期化し、ロード中のオブジェクトの複製を防止します。このプロセスはプロジェクトによって変わるものなので、その点にはご留意ください。
    # この例ではsavableオブジェクトを削除して実現します。
    var savenodes = get_tree().get_nodes_in_group("Persist")
    for i in savenodes:
        i.queue_free()

    # 行単位でファイルをロードし、ディクショナリを処理して、オブジェクトを復元する。
    var currentline = {} # dict.parse_json() requires a declared dict.
    savegame.open("user://savegame.save", File.READ)
    while (!savegame.eof_reached()):
        currentline.parse_json(savegame.get_line())
        # First we need to create the object and add it to the tree and set its position.
        var newobject = load(currentline["filename"]).instance()
        get_node(currentline["parent"]).add_child(newobject)
        newobject.set_pos(Vector2(currentline["posx"],currentline["posy"]))
        # Now we set the remaining variables.
        for i in currentline.keys():
            if (i == "filename" or i == "parent" or i == "posx" or i == "posy"):
                continue
            newobject.set(i, currentline[i])
    savegame.close()
```

これらの関数を使えば、シーンツリーのほぼどこからでも、任意の数のオブジェクトに対してセーブ/ロードが出来ます。各オブジェクトは、保存する必要があるものだけをピックアップしてセーブできます。

### 注意事項

このチュートリアルで学んだことはセーブ/ロードの基本に過ぎません。ゲームによってこのプロセスはいくらでも複雑化します。ゲームの状態を一元管理することはなかなか難しく、個々のプロジェクトにおいて大幅なカスタマイズが必要である場合もあります。

この実装では、Persistオブジェクトは他のPersistオブジェクトの子では無いものとしています。もしこの前提が崩れれば、無効なパスが生成されてしまいます。もしそのようなことがしたい場合には留意する必要があります。子オブジェクトがロードされた時にも利用出来るように、オブジェクトを親から先に段階的に保存すると、add_child()を呼び出すだけでセーブ処理が実現可能になります。ノードのパスが無効になることを防ぐため、子ノードを親ノードにリンクするという処理も必要になります。複雑ですね。
