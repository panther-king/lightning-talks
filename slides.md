---
# You can also start simply with 'default'
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: マイナー言語に学ぶ設計のヒント
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# take snapshot for each slide in the overview
overviewSnapshots: true
---

# マイナー言語に学ぶ<br>設計のヒント

堅牢で高品質なアプリケーションを目指して

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

---
transition: fade-out
---

# Motivation

アプリケーションの品質追求は永遠のテーマ

<v-click>

「優れたアプリケーション」とはどんなものでしょう？<br>
定義には様々な観点・意見があるでしょうが、領域を問わず共通認識となる指標はあるはずです。

</v-click>

<v-click>

- 堅牢
  - 想定外の挙動が発生しないこと
- 高品質
  - 実装意図が明確で、解釈の余地が限定的であること

</v-click>

<v-click>

世に数多あるプログラミング言語から、こういったポイントにフォーカスした考え方を知ることで、
堅牢で高品質なアプリケーションを構築するヒントが見つかるかもしれません。

</v-click>

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
transition: slide-up
---

# 堅牢で高品質なアプリケーションとは？

誰もが一度は経験しているはず...

<v-click>

突然のランタイムエラー

``` text
Uncaught TypeError: a is not a function
```

</v-click>

<v-click>

- いつどこで、何が原因で起きたのか？
- 私の手元では再現しません...

</v-click>

<v-click>

エラー原因の大半は <span v-mark.circle.orange="4">考慮漏れ</span> であるものの、
コードベースが大きくなるにつれ、抜け・漏れ・想定外の発生確率も上がり、事前にすべてを検知・カバーする事も困難になります。

</v-click>

<v-click>

- 外部との通信結果が想定と異なっていた
- 「有る」と想定していたデータが、特定のケースでは「無い」

</v-click>

---
transition: slide-up
---

# Webフロントエンド専用言語「Elm」

Webアプリケーションフロントエンドに特化したAltJSなDSL

https://elm-lang.org/

<v-click>

## 特徴

1. スコープをWebフロントエンドに限定
2. ランタイムエラーの徹底的な排除
3. 副作用の徹底的な隔離

</v-click>

---
transition: slide-up
---

# 1. スコープをWebフロントエンドに限定

できる事は、Webフロントエンドで必要な処理のみ

Webフロントエンド(=ブラウザ内で動くJavaScript)以外の機能を<span v-mark.underline.orange="4">切り捨てた</span>

<v-clicks>

| ユースケース | 実現方法 |
| ---- | ---- |
| RDBに接続してクエリを発行したいな | できません |
| ユーザーにメールを送信するぞ | できません |
| ファイルI/Oはどうやるのかな？ | できません |
| 並列処理でパフォーマンスアップじゃい | できないったらできないんです |

</v-clicks>

---
transition: slide-up
layout: two-cols-header
---

# 2. ランタイムエラーの徹底的な排除

コンパイルが通れば、事実上ランタイムエラーが発生しない(※)

ランタイムエラーが発生し得るコードは<span v-mark.underline.orange="4">書けない</span>

::left::

<v-clicks>

- null/undefinedのような概念が存在しない
  - 結果が無いことを表す `Maybe(Just/Nothing)`
  - 成功・失敗を表す `Result(Ok/Err)`
- 境界チェック・存在チェックを省略できない
  - List(配列)の要素取得
  - Dict(辞書)のキー参照
- 例外機構が存在しない
  - 大域脱出できない
  - エスケープハッチも存在しない

</v-clicks>

::right::

<v-click>

``` elm
-- list[0]のような記法は存在しない
List.head : List a -> Maybe a
List.head [1, 2, 3]  -- Just 1
List.head []         -- Nothing

-- dict["foo"]のような記法も存在しない
Dict.get : comparable -> Dict comparable v -> Maybe v
Dict.get "foo" Dict.empty  -- Nothing
Dict.fromList [("foo", "bar")]
    |> Dict.get "foo"      -- Just "bar"

-- どちらのケースも記述しないとコンパイルできない
type Maybe a
    = Just a
    | Nothing  -- unwrap() や panic!() は存在しない

type Result error value
    = Ok value
    | Err error  -- unwrap() や panic!() は存在しない
```

</v-click>

---
transition: slide-up
layout: two-cols-header
---

# 3. 副作用の徹底的な隔離

TEA(The Elm Architecture)による副作用や状態の管理

副作用はすべて Elm Runtime で管理されるため、アプリケーションコードは<span v-mark.underline.orange="4">常に純粋</span>

::left::

<v-clicks>

- 状態変更のトリガーを宣言
  - ボタンをクリック・テキストボックスに入力
  - 一定時間が経過する
- 発生し得る事象を宣言
  - アプリケーションの状態(Model)が変化する
  - HTTP通信が発生する・結果が返ってきた
  - さらなる副作用が発生する
- 状態に応じたDOMを宣言
  - Modelのプロパティが `foo` である
  - HTTP通信が成功・失敗した

</v-clicks>

::right::

<v-click>

<img border="rounded" src="/tea.jpg" alt="">
<small>https://kindsonthegenius.com/elm/elm-introduction-to-elm/</small>

</v-click>

---
transition: slide-up
layout: two-cols-header
---

# Elmのアプローチ

何を選んだのか、あるいは何を選ばなかったのか

<v-click>

- ドメインを明確にし、本質でない概念・機能はすべて削ぎ落とした
- 安全性に振り切って、できる事を増やすよりもそれぞれの質を磨きこんだ
- 明確なコンテキストと適切な制約の下で、最大限の自由を提供した

</v-click>

::left::

<v-click>

## 手に入れたもの

- コンパクトかつシンプルな言語仕様
- TEAという完成されたアーキテクチャ
- ランタイムエラーからの解放
- 純粋性(Immutable, Testable, Composable)

</v-click>

::right::

<v-click>

## トレードオフ

- Web GUI以外では使えない
- 選択肢が少ない
- 「今は妥協する」作戦がとれない
- 融通が利かない

</v-click>

---
transition: slide-up
---

# マイナー言語「に」学ぶ

そのエッセンスは、明日からの設計に活かせるかもしれない

<v-click>

- ドメイン(対象領域)に対する、飽くなき思索と洞察
- 大胆な取捨選択と、徹底的に強固なガードレール
- 確立された世界観の下で、選んだものの効果を最大限に高める

</v-click>

---
layout: end
---

より堅牢で高品質なアプリケーションを
