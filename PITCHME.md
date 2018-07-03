<style type="text/css">
<!--
.reveal h2 {
    text-transform: none;
    font-size: 42px;
}
.reveal p { font-size: 30px; }
.reveal ul li { font-size: 30px; }
-->
</style>

## Elm入門ハンズオン

![](./elm-logo.png)

The Elm Architecture編

[@ababupdownba](https://twitter.com/ababupdownba)

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
- 付録
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

elmがHTMLを生成し、*index.html*のdivタグに埋め込まれる。

index.js
```js
import './main.css';
import { Main } from './Main.elm';

Main.embed(document.getElementById('root'));
```

+++

## プロジェクト構成

- view関数では、HTML(DOM)を生成している
- 好きなメッセージに変えてみよう
- (他の関数も眺めてみよう)

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

## Html msg

ここからは、Elm上でHTMLを表現する*Html msg*型について見ていく。

- HTMLのプレーンな文字列(aタグの中身等)は、*text*関数で表す
- 空の要素を表すには、*text ""*と表記する

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

## Html msg

タグを表す関数は、属性のリストと*Html msg*のリストの2引数を受け取り、自身も*Html msg*という型で表さる。

```elm
-- モジュールのimport
import Html exposing (Html, text, div, h1, img, p) -- pタグを追加
import Html.Attributes exposing (src) -- 属性を増やす場合こちら

-- p : List (Attribute msg) -> List (Html msg) -> Html msg
view : Model -> Html Msg
view model =
    p [ id "foo", class "bar" ] [text "hello"]
```

```html
<p id="foo", class="bar">hello</p>
```

+++

## Html msg

divタグを表す関数の第2引数(*List (Html msg)*)にh1, pを渡す。

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

Model(状態)とview関数の連携について見ていく。

- 型aliasでModelを定義し、init関数で初期値を定義する
- viewは、Modelを用いてHtml msg型の値を返す

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

Eventは、viewからMsgを発行し処理を促す手段である。Msgを発行し、Modelを書き換えてみる。モジュールのimportとModelの定義は以下(次ページへ続く)。

```elm
import Html exposing (Html, text, div, h1, input)
import Html.Attributes exposing (type_, value)
import Html.Events exposing (onClick) -- 今後Eventを増やす場合はこちら

type alias Model =
    { word : String }


init : ( Model, Cmd Msg )
init =
    ( { word = "" }, Cmd.none )
```

+++

## EventでModelを書き換える

- typeでMsgを定義(*Press*)、updateで分岐・modelを更新
- view関数のonClick関数(Event)がMsgを発火

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

*onInput*を用いて、input要素からの入力を受け取るとする。しかし、この様な形にするとコンパイルエラーが生じる(次ページへ続く)。

```elm
type Msg
    = NoOp

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        NoOp ->
            ( model, Cmd.none )

view : Model -> Html Msg
view { word } =
    div []
        [ input [ type_ "text", onInput NoOp ] []
        , h1 [] [ text word ]
        ]
```

+++

## EventでModelを書き換える

onInputは、Stringを受け取ってMsgを返す関数を求める。NoOp(No Operation)は、Stringを受け取るMsgではない。

```elm
The argument to function `onInput` is causing a mismatch.

42|                                                 onInput NoOp
                                                            ^^^^
Function `onInput` is expecting the argument to be:

    String -> Msg

But it is:

    Msg
```

+++

## EventでModelを書き換える

- Stringを受け取るMsg(NewWord String)を定義する
- case式のパターンマッチでStringを取り出し、modelのレコードを更新する
- onInputでは、ラムダ式(無名関数)を用いてMsgを発火する

```elm
type Msg
    = NewWord String

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        NewWord w ->
            ( { model | word = w }, Cmd.none )

view : Model -> Html Msg
view { word } =
    div []
        [ input [ type_ "text", onInput (\w -> NewWord w) ] []
        , h1 [] [ text word ]]
```

+++

## List (Html msg)

li等の複数のタグをList Stringの値から生成したいとする。

```elm
view : Model -> Html Msg
view model =
    ul []
        [ li [] [ text "foo" ]
        , li [] [ text "bar" ]
        , li [] [ text "hoge" ]
        ]
```

+++

## List (Html msg)

- ModelでList Stringを定義し、initで初期データを入れる
- List Stringをliのリスト、つまりList (Html Msg)に変換する(words2li)
- view関数でwords2liを呼び出す

```elm
type alias Model =
    { words : List String }

init : ( Model, Cmd Msg )
init =
    ( { words = [ "foo", "bar", "hoge", "fuga" ] }, Cmd.none )

view : Model -> Html Msg
view { words } =
    ul [] (words2li words)

words2li : List String -> List (Html Msg)
words2li words =
    List.map (\w -> li [] [ text w ]) words
```

+++

## List (Html msg)

let式を用いれば、ローカル関数として定義できる。

```elm
view : Model -> Html Msg
view { words } =
    let
        words2li words =
            List.map (\w -> li [] [ text w ]) words
    in
        ul [] (words2li words)
```

+++

## List (Html msg)

- 1-100の整数から偶数だけを取り出し、liに変える処理をする
- あれだけど大変・・・読みづらい・・・

```elm
view : Model -> Html Msg
view { words } =
    let
        evenList =
            List.map (\x -> li [] [ text x ])
                (List.map (\x -> toString x)
                    (List.filter (\x -> x % 2 == 0)
                        (List.range 1 100)
                    )
                )
    in
        ul [] evenList
```

+++

## List (Html msg)

そんなときはパイプ演算子(|>)で、データが流れるように

```elm
view : Model -> Html Msg
view { words } =
    let
        evenList =
            List.range 1 100
                |> List.filter (\x -> x % 2 == 0)
                |> List.map toString
                |> List.map (\x -> li [] [ text x ])
    in
        ul [] evenList
```

---

## Incremental Search

このスライドと参考URLを元に解いてみよう。検索条件は、部分一致とする。答えは、テンプレートリポジトリの*answer*ブランチに。

- [テンプレート](https://github.com/ababup1192/elm-incremental-search)
- Elmドキュメント
    - [Html](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html)
    - [String](http://package.elm-lang.org/packages/elm-lang/core/latest/String)
    - [List](http://package.elm-lang.org/packages/elm-lang/core/latest/List)

+++

## Incremental Search 発展問題

チャレンジャーなあなたは次の問題にも挑戦してみよう！

- 表示件数の表示
- 部分一致・前方一致・後方一致を切り替える
- 一致部分を太文字にする

---

## おまけ - Unit Test

Elmはイミュータブル(不変)なデータ構造を主に扱う言語である。そのため関数は必ず値を返す。そのため、Unit Testを書くのは容易である。今回のIncremental Searchを実装するために、以下のシグネチャの関数を用意したとする(次のページへ続く)。

Main.elm
```elm
{-| 部分一致した文字列のみをフィルタリングする
-}
partialMatch : List String -> String -> List String

{-| 文字列をliでラップする
-}
wordToListItem : String -> Html Msg
```

+++

ユニットテストは、*test*関数の引数に、descriptionと*Expect.equal*の戻り値を渡すことで書ける。

Tests.elm(要スクロール)

```elm
[ test "partialMatch empty words" <|
    \_ ->
        let
            matchedWords =
                partialMatch [] "ab"
        in
            Expect.equal [] matchedWords
, test "partialMatch empty contain word" <|
    \_ ->
        let
            matchedWords =
                partialMatch [ "a", "b", "ab", "abc" ] ""
        in
            Expect.equal [ "a", "b", "ab", "abc" ] matchedWords
, test "partialMatch" <|
    \_ ->
        let
            matchedWords =
                partialMatch [ "a", "b", "ab", "abc" ] "b"
        in
            Expect.equal [ "b", "ab", "abc" ] matchedWords
, test "wordToListItem" <|
    \_ ->
        let
            listItem =
                wordToListItem "item"
        in
            Expect.equal (li [] [ text "item" ]) listItem
]
```

+++

## おまけ - 気になったこと

次のことが気になった方は、是非Elmを継続してやってみよう！

- このおまじないは、なんだったんだろう
     - subscriptions = always Sub.none
- 副作用はどう扱うんだろう？
- JavaScriptを呼び出したい
- SPAしてみたい
- WEB APIを呼び出したい
- プロジェクトの規模が大きくなっても破綻しない？

---

## 付録 - 関数

```elm
> stdBMI = 22.0
> bmi h = (h ^ 2) * stdBMI
<function> : Float -> Float
> bmi 1.75
(1.75 ^ 2) * stdBMI
3.0625 * 22.0
67.35
```
@[1](22.0(Float)を返す関数。変数ではない。その為、再代入不可能。)
@[4-7](関数bmiは以下のように評価されます。)
@[2,4](関数bmiの束縛変数(仮引数)hを実引数1.75で束縛します。)
@[4-5](本体の式を評価していきます。束縛し評価することを「関数適用」と呼びます。)
@[6-7](あとは、stdBMIを評価した値を束縛し、計算式を評価していきます。)
@[1-7](グローバル変数・一時変数・キーボード入力・ファイル読み込みなどは、関数に一切含まれない。そのような関数を「純粋関数」と呼びます。)

+++

## 付録 - 関数

```elm
> (\h -> (h ^ 2) * stdBMI) 1.75
67.375 : Float
> bmi = (\h -> (h ^ 2) * stdBMI)
```
@[1-2](このように関数から名前を取り除き、)
@[1-2](\変数 -> 本体の式)
@[1-2](このカタチを匿名関数(ラムダ式、ラムダ関数、ラムダ抽象)と呼びます。)
@[3](λ抽象自体も式なので名前を付けられます。)
@[1-3]( 後述しますが、式なので別の関数や式に渡すことも、関数の結果としてλ抽象を返すこともできます。
@[1-3](このような特徴を持った言語の関数を「第一級関数」(ファーストクラスファンクション)と呼びます。)

+++

## 付録 - List

```elm
> [1, 2, 3, 4, 5]
[1,2,3,4,5] : List number
> [1, "a"]
      ^^^
The 1st entry has this type:
    number
But the 2nd is:
    String
> []
[] : List a
```
@[1-2](Listを表すリテラルです。リストはある型の要素が0個以上の集まってできる型です。)
@[3-8](リストに入る型は任意ですが、リスト全体で単一の型で無ければなりません。)
@[9-10](REPL上では空のリストを作った場合に、どのような型かを予測することはできません。)
@[9-10](Elmでは任意の型を表すとき「a, b, c, ...」のように、予約語を除いた小文字から始まる**型変数**を利用します。)
@[9-10](他の言語で言う、ジェネリクスにおける型パラメータなどと同じ意味を持ちます。)

+++

## 付録 - レコード

```elm
> record = {x = 1, y = 2, z = 3}
{ x = 1, y = 2, z = 3 } : { x : number, y : number1, z : number2 }
> record.x
1 : number
> .y record
2 : number
> { record | x = 10, z = record.y + 20 }
{ x = 10, y = 2, z = 22 } : { y : number1, x : number, z : number1 }
```
@[1-2](レコードはkeyとvalueからなるセットです。JavaScriptのオブジェクトに似ています。)
@[3-4](JavaScriptのオブジェクトのように、*record*.x のようにアクセスできます。)
@[5-6](また、*.field*のような関数がレコードに対して生成されます。)
@[7-8]({ レコード変数 | 変数束縛 } の形でレコードの値を変更した、新しいレコードを生成します。)

+++

## 付録 - タプル

```elm
> (1, 2, 3, 4, 5)
(1,2,3,4,5) : ( number, number1, number2, number3, number4 )
> (1, "1", True, [(1, 2), (3, 4)], [["a", "b"], ["c", "d"]])
(1,"1",True,[(1,2),(3,4)],[["a","b"],["c","d"]])
    : ( number
      , String
      , Bool
      , List ( number1, number2 )
      , List (List String)
      )
> ()
() : ()
```
@[1,2](タプルを表すリテラルです。タプルは複数の型の0個以上の要素からなる型です。)
@[1,2](numberのように不定の型の場合、要素ごとに別物として表すため違うnumber型として推論されます。)
@[3-10](複雑なタプルの例)
@[11-12](空のタプルは**Unit**とも言われ、何もない型を表すときに使われます。)

+++

## 付録 - Type Alias

```elm
> type alias Name = String
> type alias Age = Int
> type alias User = { name: Name, age: Age }
-- getName : User -> Name
> getName user = user.name
<function> : { b | name : a } -> a
getName (User "john" 15)
"john" : Repl.Name
```
@[1-3](可読性の高いコードを書くためには、*type alias*で型の別名を付けることもできます。Elmでは型の設計をおこなってから実装を考える型駆動開発の考え方も有効です。)
@[3,7](レコード型のtype aliasは少し特殊な事情があります。(レコードalias フィールドの値, フィールド値, ...) のように本来の書き方をショートカットできます。)

+++

## 付録 - Union Types

```
> type Money = Dollar | EURO | JPY
> Dollar
Dollar : Money
> EURO
EURO : Money
> JPY
JPY : Money
> JPY == JPY -- Union Typesは等価性を備えています。
True : Bool
```
@[1](UnionTypesは、stringやbool, nullをよりも直感的に分岐した型を表すことができます。)
@[1](Tagged UnionやADT(Algebraic Data Type: 代数的データ型)と呼ばれます。)
@[1](Dollar, EURO, JPY はMoney型を返す関数で値コンストラクタと呼ばれます。)


+++

## 付録 - Union Types

```
toJPYRate : Money -> Float
toJPYRate money =
    case money of
        Dollar ->
            109.134

        EURO ->
            129.462

        JPY ->
            1
```
@[3-11](すべての分岐が無ければコンパイルエラー。つまり網羅性を保証しています。)

+++

## 付録 - ハンズオン文法編

付録でも足らない方は、[こちら](https://gitpitch.com/ababup1192/elm-handson1)

---

## アンケート

フィードバックのために[アンケート](https://docs.google.com/forms/d/e/1FAIpQLSfm9S54qv0yj8kzE49sDQmr9N8_bPAhH6JroKUoiE_dR4tOpg/viewform)にご協力お願いします。
