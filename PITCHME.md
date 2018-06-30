## Elm入門ハンズオン

![](./elm-logo.png)

The Elm Architecture編

著者: [@ababupdownba](https://twitter.com/ababupdownba)

---

### Elmってどんな言語？

- 信頼できるWebアプリのためのめっっっっっちゃ楽しい言語(Elm公式ページ意訳)
    - AltJavaScript
    - 純粋関数型
    - 言語内フレームワーク
    - 実行時例外は原則起きない

---

### このハンズオンのターゲット

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

Main.elm
```elm
view : Model -> Html Msg
view model =
    div []
        [ img [ src "/logo.svg" ] []
        , h1 [] [ text "Your Elm App is working!" ]
        ]
```

+++

## 式とは

- 何でしょうか？
    - 評価されることで値になるもの|
    - 値も式|
- 利点はなんでしょうか？|
    - 文とは違い、式は式に連続して渡すことができる|
        - 一時変数を減らす(名付けのコストを減らす)|
    - テスタビリティが上がる|
        - 純粋関数の為 式(を評価した値)の等価性が保証される|
---

## リテラル: Bool, number

```elm
> True
True : Bool
> False
False : Bool
> 42
42 : number -- IntもしくはFloatである型
> 3.14
3.14 : Float
```

+++

## リテラル: Char, String

```elm
> 'a'
'a' : Char
> "abc"
"abc" : String
> """\
| hello\
| world\
| """
"\n  hello\n  world\n  " : String
```
@[5-8](REPLで複数行入力するには、\を行末に付けます。)

+++

## リテラル: List

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

## リテラル: タプル

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

## リテラル: レコード

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
@[7-8]({ レコード変数 | 変数束縛 } の形でレコードの値を変更した、新しいレコードを生成します。)

+++

## リテラルまとめ

- リテラルは直接記述することのできる式です。そのままの形が評価されて値となります
- 複数の要素を扱うリテラルで記述できるデータ構造はList, タプル, レコードがあります。

+++

## リテラルまとめ
    
 - Listは単一の型の要素しか扱えませんが豊富な関数が用意されており、最も使われるデータ構造の一つです
 - タプルは複数の型を混在して扱えますが、汎用性は高くありません
 - レコードは所謂Getter, Setterが用意されており、Objectのように使える便利な型です

+++

## リテラル: プチ演習

- ```name = { firstName = "Donald", lastName = "Trump" }``` レコードからフルネーム(スペース区切りの名前)を生成してみましょう。(ヒント: 文字列の結合は、(++)で出来ます)

+++

## リテラル: プチ演習

- johnの型を考えてみましょう。
- johnのageを17にeyesightを(0.6, 0.7)にしてみましょう。

```elm
john =
    { name = "John"
    , age = 15
    , eyesight = ( 0.7, 0.8 )
    , family =
        [ { name = "Mary", age = 20, eyesight = ( 1.0, 1.0 ) }
        , { name = "Mike", age = 23, eyesight = ( 0.5, 0.5 ) }
        ]
    }
```

---

## 関数

```elm
> square n = n^2
<function> : number -> number
> square 5
> 25
```
@[1](function arg1 arg2,... = body)
@[2](number型の値を受け取り、number型の値を返す関数。)
@[3](関数squareの束縛変数(仮引数)nを実引数5で束縛します。)
@[3-4](本体が評価されることで、25となります)
@[1-4](関数定義と評価)

+++

## 関数

複数行のプログラムは「Handson.elm」に関数を書き、それを読み込みましょう。

```elm
module Handson exposing (..) -- モジュールと言う単位で関数を公開します。

square : Int -> Int -- ドキュメンテーションのための型シグネチャ
square n =
    n ^ 2
```

```elm
> import Handson exposing (..) -- REPLでモジュールをimport
> square 5
25 : Int
```

+++

## 関数

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
@[1-3](可読性の高いコードを書くためには、*type alias*で型の別名を付けることもできます。Elmでは型の設計をおこなってから実装を考える型駆動開発の考え方も有効です。)
@[3,7](レコード型のtype aliasは少し特殊な事情があります。(レコードalias フィールドの値, フィールド値, ...) のように本来の書き方をショートカットできます。)

+++

## 関数

```elm
> type Name = Name String
> type Age = Age Int
> type alias User = { name: Name, age: Age }
> User (Name "john") (Age 15)
{ name = Name "john", age = Age 15 } : Repl.User
> User "john"
Function `User` is expecting the 1st argument to be:

    Name

But it is:

    String
```
type aliasは型の別名を付けたに過ぎないため、*type*キーワードを使うことで型を作り出し、可読性を高くしつつ、さらに型安全にすることができます。

+++

## 関数

```elm
> type Container a = Container a
> Container 1
Container 1 : Repl.Container number
> Container "abc"
Container "abc" : Repl.Container String
> Container (1, "abc")
Container (1,"abc") : Repl.Container ( number, String )
```

@[1-7](typeで作り出した型はListのように型変数を持つことが出来ます。)
@[1-2](値を受け取ることで、それ自身の型を返す関数を作り出します。これを値コンストラクタと呼びます。)


+++

## 関数

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

## 関数

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

## 関数

Elmドキュメント: [Basics](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Basics)パッケージ の組み込み関数を試してみましょう

```elm
> negate 5
-5 : number
> abs -10
10 : number
> sqrt 2
1.4142135623730951 : Float
> round 4.5
5 : Int
> max 1 5 -- 2引数以上受け取る場合にはスペース区切りで渡します。
5 : number
```

+++

## 関数

```elm
> 10 + 5 -- 二項演算子は中置記法が使える関数です。
15 : number
> (+) 10 5 -- 丸括弧で括ることで、通常の関数のように前置記法で記述できます。
15 : number
> True && False
False : Bool
> 1 < 3
True : Bool
> 1 == 2
False : Bool
> 1 /= 2
True : Bool
```

+++

## 関数

```elm
-- (/) : Float -> Float -> Float
> 5 / 2
2.5 : Float
— (//) : Int -> Int -> Int
> 5 // 2
2 : Int
-- 2はFloatとみなされる
> 5.5 / 2
2.75 : Float
> 5 // 2.5 -- TYPE MISMATCH
```

+++

## 関数まとめ

- 関数は、変数束縛することで式となり評価され値になります
- 関数はシグネチャを明示することでドキュメントの代わりになります
- 型にエイリアスを張ったり独自の型を作ることで、可読性を上げたり型安全にすることができます
- 二項演算子も2引数を受け取る記号からなる関数に過ぎません

+++

## プチ演習: 関数

- Elmドキュメント: [Basicsパッケージ](http://package.elm-lang.org/packages/elm-lang/core/latest/Basics) の関数をいくつか試してみましょう
- λ抽象で関数を定義してみましょう

+++

## let式

```elm
> let a = 10 in a + 5
15 : number
> let px x = toString x ++ "px" in (px 10, px 5)
("10px","5px") : ( String, String )
```
@[1-2](let式は、let *変数束縛* in *式*と言うように書きます。スコープを限定し式なのでそのまま評価されます。)
@[2-3](let式の特徴として、変数束縛時にローカル関数を定義することができます。)


+++

## let式

```elm
circle r =
    let
        pi =
            3.14

        circumference =
            2 * pi * r

        area =
            pi * r ^ 2
    in
        "Circle(circumference = " ++ (toString circumference) ++ ", area = " ++ (toString area) ++ ")"

<function> : Float -> String
> circle 3
"Circle(circumference = 18.84, area = 28.26)" : String
```
実際には、let式は関数中でこのように使われます。

+++

## let式まとめ

- let式を使うことでスコープを制限した関数群を式に利用することができ、安全かつ見通しをよくすることができます
- letは式なのでネストすることができます

+++

## プチ演習: let式

- let式同士の演算をしてみましょう
- let式を用いた関数を定義してみましょう

---

## if式

```elm
> powerLevel = 10000
10000 : number
> if powerLevel > 9000 then "OVER 9000!!!" else "meh"
"OVER 9000!!!" : String
> String.length (if powerLevel > 9000 then "OVER 9000!!!" else "meh")
12 : Int
```
@[3-4](ifは文ではなく式のため評価されることにより値になり、else節の省略は出来ません。)
@[5-6](値なので、別の関数の引数として渡すことも可能)

+++

## if式

```elm
pushKey key n =
    if key == 40 then
        n + 1
    else if key == 38 then
        n - 1
    else
        n

> pushKey 40 5
6 : number
```

@[4-10](else節にif式をネストすることも可能。大きくスペースを取るインデントも見やすい。)

+++

## if式まとめ

- ifはelse節とセットで値を確実に返す式として定義できます
- ifは式なので、ネストさせることができます

+++

## プチ演習: if式

- if同士の演算(if式同士の足し算など)を試してみましょう。

---

## パターンマッチ

```elm
fib n =
    case n of
        0 ->
            1

        1 ->
            1

        _ ->
            fib (n - 1) + fib (n - 2)

> fib 10
89 : number
```
パターンマッチの本質は分岐をおこなうswitch-case文とは大きく異なります(次ページ)。

+++

## パターンマッチ

```elm
> tuple = (8, 4)
(8, 4) : ( number, number1 )
> case tuple of
|     ( x, y ) ->\
|         x + y
12 : numbner

record = { x = 7, y = 8 }
> case record of\
|     { x, y } ->\
|         x + y
15 : number
```
パターンマッチの本質はデータ構造の分解です。データ構造が構造通り直感的に取り出せていることがわかります。

+++

## パターンマッチ

```elm
> plusPair (x, y) = x + y
<function> : ( number, number ) -> number
> plusPair (4, 4)
8 : number
> (\(x, y) -> x + y) (4, 4)
8 : number
> let {x, y} = { x = 4, y = 4 } in x + y
8 : number
> let ({x, y, z} as record) = { x = 1, y = 2, z = 3 } in { record | y = x * y, z = x + z }
{ x = 1, y = 2, z = 4 } : { x : number2, y : number, z : number1 }
```
@[1-8](分岐の存在しないデータ構造に関しては、関数・無名関数・let式等でパターンマッチして取り出すことができます。)
@[9-10](パターンマッチの際に分解前のデータ構造そのものを扱いたいときには、**as**キーワードを使い変数に束縛します。もちろんlet式以外でも使えます。)

+++

## パターンマッチまとめ

- case式はデータ構造を分解し、直感的に扱うことができます
- caseは式のためネストさせることができます
- 分岐の無いデータ構造の場合、関数やラムダ式、let式でも同様にパターンマッチをすることができます

+++

## プチ演習: パターンマッチ

- ```name = { firstName = "Donald", lastName = "Trump" }``` レコードからフルネーム(スペース区切りの名前)を生成してみましょう。
- ```{ x = 10, y = ( 1, 5 ) }``` レコードとタプルの数字の合計(16)を一つのlet式で表してみましょう。

---

## Union Types

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

## Union Types

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

## Union Types

```elm
> type Foo = Bar Int
> getBarNum (Bar num) = num
<function> : Repl.Foo -> Int
> getBarNum (Bar 5)
5 : Int
```
分岐の無いUnion Typeの場合は関数の引数でパターンマッチできます。括弧を忘れずに!

+++

## Union Types

値があるか無いかの分岐に使う[Maybe](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Maybe)型です。

```elm
type Maybe a  -- Container a のように型変数を持ちます。
    = Just a
    | Nothing
```

+++

## Union Types

```elm
-- getNumOrZero : Maybe Int -> Int
> getNumOrZero mNum =
    case mNum of
        Just n ->
            n

        Nothing ->
            0

> let f x = if x > 5 then Just x else Nothing in getNumOrZero(f 10)
10 : number
> let f x = if x > 5 then Just x else Nothing in getNumOrZero(f 3)
0 : number
> 
```

+++

## Union Types

[Result](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Result)型は、Maybeと似た型ですが、失敗したときの理由を任意の型で受け取ることができます。

```elm
> safeSqrt n =
    if n >= 0 then
        Ok n
    else
        Err "n must be a positive number."

> safeSqrt 1
Ok 1 : Result.Result String number
> safeSqrt -1
Err "n must be a positive number." : Result.Result String number
```

+++

## プチ演習: Union Types

- Union Typesを用いて自分の型を定義してみましょう。
- 定義した型に対して関数を実装してみましょう。

---

## 再帰

```elm
> []
> [1]
[1] : List number
> [1] == 1 :: []
True : Bool
> (::) 1 []
[1] : List number
> [1, 2, 3] == 1 :: 2 :: 3 :: [] 
True : Bool
```
@[1](再帰的な関数の例を扱う定番はListです。Listは再帰的なデータ構造のためです。仮に、この空のリストをList Int型のリストとみなしましょう)
@[2-3](単一要素のリストは、1 :: [](先頭要素 :: 空のリスト)と見なすことができます。)
@[4-5](実際にリストは 1 :: []のシンタックスシュガー(糖衣構文)のため、等価です。)
@[6-7]([(::)](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/List#::)は、リストの先頭に新しい要素を追加した新しいリストを返す演算子です。)
@[8-9](それ以上の長さのリストは、(::)演算子で数珠つなぎになっており、右から順に評価されて一つのリストになります。)

+++

## 再帰

```elm
myHead : List a -> a 
myHead list =
    case list of
        [] ->
            Debug.crash "No such Element"

        x :: xs -> -- xsは使用しないため _ で置き換えて良い。
            x

> myHead [1, 2, 3]
1 : Number
> myHead []
-- crash
```
@[4-5,7-8](リストを順に処理したい場合には、このパターンに分岐されます。)
@[8-9](xsは先のページから、List a 型になることは分かります。このxsは使われていないため、再帰はしていないケースになります。)
@[5,12-13](Debug.crashは、例外を発生させる関数です。本来はTODO用途で使いましょう。)
@[1-13](実際のList.headなど通常の組み込み関数では実行時例外は起きません。確認してみましょう。)

+++

## 再帰

```elm
sum : List Int -> Int
sum list =
    case list of
        [] ->
            0

        x :: xs ->
            x + sum xs

> sum []
0
> sum [1]
1 : number
> sum [1, 2, 3]
6
```
@[7-8](パターンマッチは先ほどの例と変わり有りません。代わりに残りのリストxsが再帰的に呼ばれています。)
@[4-5,10-11]([]のときは、当然0が返ります。)
@[7-8, 12-13](1 :: [] にマッチして 1 + 0 = 1 となります。)
@[7-8,14-15](順に計算すると、1 + (2 + (3 + 0)) = 6 となります。)

+++

## プチ演習: 再帰

- [Listパッケージ](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/List)の関数を試してみましょう。
- List.isEmpty, List.tail をパターンマッチを利用して自分で定義してみましょう。
    - myIsEmpty, myTailという名前で定義してみましょう。
- Listの積を求めてみましょう。
- List Int のすべての要素を2倍したList Intを返す関数を書いてみましょう。

---

## 高階関数

---

## モジュール

---

## 応用テクニック

List.extraとかLensとかTreeとか
