<style type="text/css">
<!--
.reveal h2 {
    text-transform: none;
    font-size: 36px;
}
.reveal p { font-size: 30px; }
.reveal ul li { font-size: 30px; }
-->
</style>

## Elm入門ハンズオン

![](./elm-logo.png)

The Elm Architecture編

@ [ababupdownba](https://twitter.com/ababupdownba)

---

## Elmってどんな言語？

- 信頼できるWebアプリのためのめっっっっっちゃ楽しい言語(Elm公式ページ意訳)
    - AltJavaScript
    - 純粋関数型
    - 言語内フレームワーク
    - 実行時例外は原則起きない

---

## このハンズオンのターゲット

- Elmを一切知らない方
    - 始めたばかりで右も左もわからない方
- フロントエンド開発が初めての方
    - サーバサイド・インフラの人でも！

---

## このハンズオンの目的

- Elmの考えを引き出しの一つにしてもらいたい
    - 他の言語でも、Elmの思考は必ず役に立ちます！
    - 個人でもプロダクトでも選択肢の一つに！
- Elmで気持ちよくなってもらいたい
    - 今までの言語と脳の使い方が違う(かも?)、だけどきもちい！！！

---

## 目次

- 環境構築
- The Elm Architecture
- 実践 The Elm Architecture
- Incremental Search演習
- おまけ
- アンケート

---

## 環境構築

- [資料](https://gist.github.com/ababup1192/a1a091bcc0e535d180544639f531302c)

---

## The Elm Architecture

- 一方通行データフロー(Reduxの元となる考え方)
- それぞれの要素は、データもしくは、関数

![TEA](https://github.com/ababup1192/elm-handson2/blob/master/img/architecture.png?raw=true)

+++

## The Elm Architecture の例

[カウンタアプリ](http://elm-lang.org/examples/buttons)

![TEA Counter](https://github.com/ababup1192/elm-handson2/blob/master/img/counter.png?raw=true)

---

## 実践 - Hello World

create-elm-appは、webpackをラップしているelmプロジェクト生成ツール

```
$ create-elm-app tea-hello
$ cd tea-hello
$ elm-app start
open browser http://localhost:3000/
```

+++

## プロジェクト構成

- Project Root
    - elm-package.json
    - elm-stuff
        - elmライブラリ群
    - src
        - elm, js, css...
    - public
        - html, ico, svg, json...
    - tests
        - Tests.elm, elm-package.json(テスト用)

+++

## プロジェクト構成

divタグのみが存在

index.html
```html
// 中略
<body>
    <noscript>
        You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
</body>
</html>
```

+++

## プロジェクト構成

elmが仮想DOMを生成し、*index.html*のdivタグに埋め込まれる。

index.js
```js
import './main.css';
import { Main } from './Main.elm';
import registerServiceWorker from './registerServiceWorker';

Main.embed(document.getElementById('root'));

registerServiceWorker();
```

+++

## プロジェクト構成

view関数では、仮想DOMを生成しています。お好きなメッセージに変えてみましょう。
(他の関数については、眺めておいてください。)

Main.elm
```elm
view : Model -> Html Msg
view model =
    div []
        [ img [ src "/logo.svg" ] []
        , h1 [] [ text "Your Elm App is working!" ]
        ]
```

---

## Html Msg

ただのHTMLの文字列は、*text*関数で表します。空の要素を表すには、*text ""*と記述します。

```elm
-- text : String -> Html msg
view : Model -> Html Msg
view model =
    text "hello"
```

```html
<!-- HTML上のテキスト -->
hello
```

+++

## Html Msg

タグを表す関数は、属性のリストと*Html msg*のリストを受け取り、自身も*Html msg*という型で表されます。

```elm
-- p : List (Attribute msg) -> List (Html msg) -> Html msg
view : Model -> Html Msg
view model =
    p [ id "foo", class "bar" ] [text "hello"]
```

```html
<p id="foo", class="bar">hello</p>
```

+++

## Html Msg

リストに複数の*Html msg*を入れてみましょう。

```elm
-- div : List (Attribute msg) -> List (Html msg) -> Html msg
view : Model -> Html Msg
view model =
    div []
        [ h1 [] [ text "Hello" ]
        , p [] [ text "elm" ]
        ]
```

```html
<div>
    <h1>Hello</h1>
    <p>elm</p>
</div>
```

+++

## Modelを用いて描画をする

アプリケーションの状態はModelで表されます。型aliasで定義をしinitで初期値を定義します。viewは、Modelを用いて描画が行われます。

```elm
type alias Model =
    { hText : String, pText : String }

init : ( Model, Cmd Msg )
init =
    ( { hText = "Hello", pText = "world" }, Cmd.none )

view : Model -> Html Msg
-- view { hText, pText } =
view model =
    div []
        [ h1 [] [ text model.hText ]
        , p [] [ text model.pText ]
        ]
```

+++

## EventでModelを書き換える

モジュールのimportとModelの定義は以下のようになります。(次ページへ続く)

```elm
import Html exposing (Html, text, div, h1, input)
import Html.Attributes exposing (type_, value)
import Html.Events exposing (onClick)

type alias Model =
    { word : String }


init : ( Model, Cmd Msg )
init =
    ( { word = "" }, Cmd.none )
```

+++

## EventでModelを書き換える

- typeでMsgを定義、updateで分岐・modelを更新
- view関数のonClick関数がMsgを発火

```elm
type Msg
    = Press

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        Press ->
            ( { model | word = "Button is pressed!" }, Cmd.none )

view : Model -> Html Msg
view { word } =
    div []
        [ input [ type_ "button", value "Press!", onClick Press ] []
        , h1 [] [ text word ]
        ]
```

+++


## EventでModelを書き換える

```elm

```

