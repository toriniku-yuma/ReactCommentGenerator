# 概要
KILINBOX氏制作の[HTML5コメントジェネレータ](https://www.kilinbox.net/2016/01/HCG.html)を参考に、Reactで作成したコメントジェネレータです。

主な機能として

- WebSocketでコメントを受信し、OBSの画面上へ表示を行う。
- 従来のコメントジェネレータ同様、リスト形式で上から下へコメントを表示する。
- コメントを[バーチャルキャスト](https://virtualcast.jp/)のようにフキダシでポップさせる。
- フキダシを被らせたくない場所を選択出来る。(現在は中央付近固定)

などの機能があります。

現在は、[MultiCommentViewer](https://ryu-s.github.io/app/multicommentviewer)と[棒読みちゃん](https://chi.usamimi.info/Program/Application/BouyomiChan/)からのコメントに対応しておりますが、WebSocketで受信したJSONテキストであれば何でも表示することが出来るので、その他アプリケーションなどからのコメントなども受信することが可能です。

参考動画

# 使い方
**※現在、α版として公開しております。そのため、設定の反映にはビルド、もしくは開発モードを使用しないといけない仕様となっております。**

<br>

1. DLしたzipファイルを解凍
2. MultiCommentViewerのフォルダ内にあるpluginsフォルダに解凍したフォルダ内のWSComeGeneフォルダをドラッグ&ドロップ。

    棒読みちゃんの場合は、BouyomiChan.exeのあるフォルダ内に解凍したフォルダ内のBouyomiPluginフォルダの**中身**をドラッグ&ドロップ。
3. OBSを起動し、ソース追加からブラウザを選択、ローカルファイルにチェックを入れた後に、解凍したフォルダ内のindex.htmlを選択し、画面サイズを配信画面のサイズと合わせる。
4. MultiCommentViewerで接続を押せばコメントがOBS画面に流れ始めます。

   棒読みちゃんの場合は、プラグイン導入後初回起動時にプラグインを有効にするかどうか聞かれますので、はいを押してください。
   ただし、現在の仕様上MultiCommentViewerと棒読みちゃんのプラグインを同時に使用することは出来ませんので、同時使用時には棒読みちゃんプラグインの方をオフにしてご使用ください。

<br>

WebSocketポートを変更したい場合、Reactコメントジェネレータ側はConfig.jsonを、プラグイン側はWebSocketConfig.jsonファイルをテキストエディタで開き、portの値を書き換えてください。

# カスタム
## α版現在のコンフィグやレイアウトの変更方法
現在、コンフィグやレイアウトを変更する際にnode.jsを使用して頂くことで対応しております。

<br>

1. [こちら](https://nodejs.org/ja/download/)からnode.jsをv18.12.1以降のバージョンでインストールしてください。
2. ビルドフォルダ内のあるcomment_generator_builder.exeを起動してください。
3. npm installの隣のGoボタンを押して頂くとパッケージのインストールが始まります。

    ここでエラー文が表示される場合、node.jsが正しくインストールされてない可能性があります。

4. 下記のconfig.jsonの編集方法を参考に、コンフィグを設定してください。
5. npm run buildの隣のGoボタンを押してください。

    ここでエラー文が表示される場合、config.jsonが間違っている可能性があります。
6. 生成されたビルド済みフォルダを開いて、中のindex.htmlをOBSのブラウザソースで選択してください。


### コンフィグの変更方法
source/srcフォルダ内のconfig.jsonを以下を参考に編集してください。

上手くbuild出来なくなる可能性があるので、config.jsonファイルは必ずバックアップを取るようにお願いします。

```json
{
    "port":23699,
    "viewType":"time",
    "viewCount":10,
    "viewTime":10000,
    "isArrayComponent":"Balloon",
    "isComponent":"Balloon",
    "isScroll":"bottom",
    "isAnim":"right",
    "ballonSVGArray":[
        "e0724_1yellow.svg",
        "e0724_1magenta.svg",
        "e0724_1lightblue.svg",
        "e0724_1green.svg"
    ]
}
```

port:WebSocketのポート番号。基本的にはデフォルトでOK。

viewType:countにするとコメントを数で保持、timeにするとコメントを時間で保持します。

viewCount:viewTypeがcountの場合、幾つまでコメントを保持するかの数

viewTime:viewTypeがtimeの場合、受信から何秒までコメントを保持し続けるかの数字。ミリ秒で入力してください。

isArrayComponent:内部での配列処理に使うコンポーネントを設定します。難しいことはさておき、ここをCommentにすればよく見る一般的なリスト式のコメントジェネレータを、Balloonにすればフキダシ式コメントジェネレータになります。

isComponent:どのタイプのコンポーネントを使うかを設定します。難しいことはさておき、

- isArrayComponentがCommentの場合、Comment1は背景があり、Comment2が背景のないものになります。
- isArrayComponentがBalloonの場合、現状は何を指定しても変わりません。適当にBalloonとかにしておいてください。

isScroll:Commentコンポーネント使用時に、コメントが上から来るか下から来るかを設定します。上からの場合top、下からの場合bottomです。

isAnim:Commentコンポーネント使用時に、コメントが右から来るか左から来るかを設定します。

balloonSVGArray:使用するフキダシ画像を/public/hukidashiの中から指定します。多分SVGじゃなくても使えます。

### レイアウトを自分でカスタマイズする方法（上級者向け）
このコメントジェネレータはReactというフレームワークで動作しており、ReactにはJSXというHTMLのように記述できる仕組みが存在します。
ですので、Comment.tsxを参考に好きなところを改変していただければ、いい感じのデザインになるかと思います。

また、CSSにはTailwindCSSというクラスに記述していくタイプのCSSのライブラリを使用しております。合わせてご利用ください。

`npm run dev`で開発モードにして頂き、URLをOBSのブラウザソースに入れるとより便利になると思います。

# トラブルシューティング
## コメントを表示してくれない場合
MultiCommentViewerとコメントジェネレータ双方のWebSocketポートが正しいか確認してください（何も操作してなければ合っているはずです）

OBSのバージョンなどを確認してください。古い場合は動かない可能性があります。

それでも動かない場合はindex.htmlの中のコードを以下のように書き換えてください。

```html
<!-- before -->
    <script type="module" crossorigin src="./index.js"></script>
<!-- after -->
    <script type="text/javascript" src="./index.js" defer></script>
```

# スペシャルサンクス
tonimono

randamGen.tsxを書いてくれたり、色々とご助言を頂きました！感謝。

