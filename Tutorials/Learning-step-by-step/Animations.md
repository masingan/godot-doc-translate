## アニメーション

### イントロダクション

このチュートリアルは、Godotの全てのアニメーションに通じる方法を説明します。Godotのアニメーションシステムは強力かつ柔軟です。

これには前回のチュートリアル、見栄えのするスクリーンのシーンを使います。シンプルなアニメーションを付加することが目標です。一応コピーを置いておきます。

[robisplash.zip](http://docs.godotengine.org/en/latest/_downloads/robisplash.zip "robisplash.zip")

### アニメーションの作成

まず、AnimationPlayerノードをルートノードbgの子ノードとして追加します。

![animplayer](./Learning-step-by-step-assets/Animations/animplayer.png)

このタイプのノードが選ばれると、アニメーションエディターパネルが現れます。

![animpanel](./Learning-step-by-step-assets/Animations/animpanel.png)

さて、新しくアニメーションを作りましょう。new animationボタンを押して、そのアニメーションに"Intro"と名付けて下さい。

![animnew](./Learning-step-by-step-assets/Animations/animnew.png)

アニメーションが作成されたら"edit"ボタンを押して、編集しましょう。

![animedit](./Learning-step-by-step-assets/Animations/animedit.png)

### アニメーションの編集

"edit"ボタンが押されると少し面白いことが置きます。アニメーションエディターがアニメーションパネルに現れます。(Godot 2.xではこのボタンは削除されました。代わりに"animation"トグルをクリックすします。)

![animeditor](./Learning-step-by-step-assets/Animations/animeditor.png)

そして次が最も重要な変化です。プロパティーエディタがアニメーション編集モードに切り替わると言うことです。このモードではキーアイコンがあらゆるプロパティに付きます。これは、Godotでは全てのオブジェクトに対してアニメーションを付加できることを意味しています。

![propertykeys](./Learning-step-by-step-assets/Animations/propertykeys.png)

### ロゴの表示アニメーションを作る

次に、ロゴをスクリーン上部から出現させます。アニメーションプレイヤーを選びぶと、エディターパネルは手動で隠さなければ表示され続けます。(アニメーションノードを消せば消えます。)これを使って、"logo"ノードを選択し、"pos"プロパティを選択、114,-400に動かします。

このポジションに移動したら、プロパティの後ろにあるキーボタンを押します。

![keypress](./Learning-step-by-step-assets/Animations/keypress.png)

トラックが新しくなると、確認ダイアログが現れます。きちんと確認しておきましょう。

![addtrack](./Learning-step-by-step-assets/Animations/addtrack.png)

アニメーションプレイヤーエディターにキーフレームが追加されます。

![keyadded](./Learning-step-by-step-assets/Animations/keyadded.png)

次に、エディタのカーソルをアニメーションの終了ラインに合わせます。ここをクリックしてください。

![move_cursor](./Learning-step-by-step-assets/Animations/move_cursor.png)

ロコの位置を(114,0)に変え、キーフレームを再び打ちます。2つキーフレームがあるとアニメーションが生成されます。

![animation](./Learning-step-by-step-assets/Animations/animation.png)

アニメーションパネルでPlayを押すとロゴが降りてきます。シーンを実行してテストがしたいので、シーンが始まる時に自動的に再生するタグをアニメーションに付けます。

![autoplay](./Learning-step-by-step-assets/Animations/autoplay.png)

最後に、シーンを再生して、アニメーションがこのように再生されたら成功です。

![out.gif](./Learning-step-by-step-assets/Animations/out.gif)

