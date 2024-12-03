---
# You can also start simply with 'default'
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: マイナー言語から学ぶ設計のヒント
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

# マイナー言語から学ぶ<br>設計のヒント

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

アプリケーションの品質担保は永遠のテーマ

「高品質なアプリケーション」とはどんなものでしょう？<br>
品質の定義には様々な観点・意見があるでしょうが、領域を問わず共通認識となる指標はあるはずです。

- 想定外の挙動が発生しないこと
- 実装意図が明確で、解釈の余地が限定的であること

世に数多あるプログラミング言語から、こういったポイントにフォーカスした考え方を知ることで、
堅牢で高品質なアプリケーションを構築するヒントが見つかるかもしれません。

今回は、Webアプリケーションフロントエンド専用言語「[Elm](https://elm-lang.org/)」にスポットを当ててみます。

- ブラウザ内で動くJavaScriptを生成するAltJS
- 実質、純粋関数型言語 [Haskell](https://www.haskell.org/) のサブセット

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

# 堅牢なアプリケーションとは？

誰もが一度は経験しているはず...

- 突然のランタイムエラー
  - いつどこで、何が原因で起きたのか？
  - 私の手元では再現しません...

``` text
Uncaught TypeError: a is undefined
```

コードベースが大きくなるにつれ、抜け・漏れ・想定外の発生確率も上がり、事前にすべてを検知・カバーする事も困難になります。
エラーの原因は __考慮漏れ__ が多いのではないでしょうか。

- バックエンドとの通信結果が想定と異なっていた
- 「有る」と想定していたデータが、特定のケースでは「無い」

---
transition: slide-up
---

# Webフロントエンド専用言語「Elm」

Webアプリケーションフロントエンドに特化したDSL(Domain Specific Language)

https://elm-lang.org/

## 特徴

- スコープをWebフロントエンドに限定
- ランタイムエラーの徹底的な排除
- 副作用の徹底的な隔離

---
transition: slide-up
---

# スコープをWebフロントエンドに限定

できる事は、Webフロントエンドで必要な処理のみ

Webフロントエンド(=ブラウザ内で動くJavaScript)以外の機能を __切り捨てた__

| ユースケース | 実現方法 |
| ---- | ---- |
| RDBに接続してクエリを発行したいな | できません |
| ユーザーにメールを送信したいな | できません |
| ファイルI/Oはどうやるのかな？ | できません |
| 並列処理でパフォーマンスを上げたいな | できないったらできないんです |

---
transition: slide-up
layout: two-cols-header
---

# ランタイムエラーの徹底的な排除

コンパイルが通れば、事実上ランタイムエラーが発生しない(※)

ランタイムエラーが発生し得るコードを __書けない__

::left::

- 境界チェック・存在チェックを省略できない
  - List(配列)の要素取得
  - Dict(辞書)のキー参照
- 例外機構が存在しない
  - 大域脱出できない
  - エスケープハッチも存在しない
- ※ランタイムエラーが発生し得るケース
  - コンパイラのバグ
  - StackOverflow

::right::

``` elm
-- list[0]のような記法は存在しない
List.head : List a -> Maybe a
List.head [1, 2, 3]  -- Just 1
List.head []         -- Nothing

-- dict["foo"]のような記法も存在しない
Dict.get : comparable -> Dict comparable v -> Maybe v
Dict.get "foo" Dict.empty  -- Nothing
Dict.fromList [("foo", "bar")]
    |> Dict.get "foo"  -- Just "bar"

-- どちらのケースも記述しないとコンパイルできない
type Maybe a
    = Just a
    | Nothing  -- unwrap() や panic!() は存在しない

type Result error value
    = Ok value
    | Err error  -- unwrap() や panic!() は存在しない
```

---
transition: slide-up
layout: two-cols-header
---

# 副作用の徹底的な隔離

TEA(The Elm Architecture)による副作用や状態の管理

副作用はすべて Elm Runtime で管理されるため、アプリケーションコードは __常に純粋__

::left::

- 状態変更のトリガーを __宣言__
  - ボタンをクリック・テキストボックスに入力
- 発生し得る事実を __宣言__
  - Modelのプロパティが変化する
  - HTTP通信が発生する・結果が返ってきた
  - さらなる副作用が発生する
- 状態に応じたDOMを __宣言__
  - Modelに特定のプロパティがある・無い
  - HTTP通信が成功・失敗した

::right::

<img border="rounded" src="/tea.jpg" alt="">

---
transition: slide-up
layout: two-cols-header
---

# Elmのアプローチ

堅牢なWebアプリケーションフロントエンドを実現するために、選んだことと捨てたもの

## 解決すべきドメインヘフォーカス

::left::

### 手に入れたもの

- コンパクトかつシンプルな言語仕様
- TEAという完成されたアーキテクチャ

::right::

### トレードオフ

- Web GUI以外では使えない
- ボイラープレートコードが増える

---
transition: slide-up
layout: two-cols-header
---

# Elmのアプローチ

堅牢なWebアプリケーションフロントエンドを実現するために、選んだことと捨てたもの

## 安全性を何よりも重視

::left::

### 手に入れたもの

- ランタイムエラーからの解放
- 品質への自信

::right::

### トレードオフ

- 「今は手を抜く」という妥協ができない
- 冗長な記述が増えがち

---
transition: slide-up
layout: two-cols-header
---

# Elmのアプローチ

堅牢なWebアプリケーションフロントエンドを実現するために、選んだことと捨てたもの

## 純粋性の追求

::left::

### 手に入れたもの

- Immutability
- Testability
- Composability

::right::

### トレードオフ

- とっつきやすさ

---
layout: center
class: text-center
---

# より堅牢で高品質なアプリケーションを

---
layout: two-cols
layoutClass: gap-16
---

# Table of contents

You can use the `Toc` component to generate a table of contents for your slides:

```html
<Toc minDepth="1" maxDepth="1"></Toc>
```

The title will be inferred from your slide content, or you can override it with `title` and `level` in your frontmatter.

::right::

<Toc v-click minDepth="1" maxDepth="2"></Toc>

---
layout: image-right
image: https://cover.sli.dev
---

# Code

Use code snippets and get the highlighting directly, and even types hover!

```ts {all|5|7|7-8|10|all} twoslash
// TwoSlash enables TypeScript hover information
// and errors in markdown code blocks
// More at https://shiki.style/packages/twoslash

import { computed, ref } from 'vue'

const count = ref(0)
const doubled = computed(() => count.value * 2)

doubled.value = 2
```

<arrow v-click="[4, 5]" x1="350" y1="310" x2="195" y2="334" color="#953" width="2" arrowSize="1" />

<!-- This allow you to embed external code blocks -->
<<< @/snippets/external.ts#snippet

<!-- Footer -->

[Learn more](https://sli.dev/features/line-highlighting)

<!-- Inline style -->
<style>
.footnotes-sep {
  @apply mt-5 opacity-10;
}
.footnotes {
  @apply text-sm opacity-75;
}
.footnote-backref {
  display: none;
}
</style>

<!--
Notes can also sync with clicks

[click] This will be highlighted after the first click

[click] Highlighted with `count = ref(0)`

[click:3] Last click (skip two clicks)
-->

---
level: 2
---

# Shiki Magic Move

Powered by [shiki-magic-move](https://shiki-magic-move.netlify.app/), Slidev supports animations across multiple code snippets.

Add multiple code blocks and wrap them with <code>````md magic-move</code> (four backticks) to enable the magic move. For example:

````md magic-move {lines: true}
```ts {*|2|*}
// step 1
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})
```

```ts {*|1-2|3-4|3-4,8}
// step 2
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
}
```

```ts
// step 3
export default {
  data: () => ({
    author: {
      name: 'John Doe',
      books: [
        'Vue 2 - Advanced Guide',
        'Vue 3 - Basic Guide',
        'Vue 4 - The Mystery'
      ]
    }
  })
}
```

Non-code blocks are ignored.

```vue
<!-- step 4 -->
<script setup>
const author = {
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
}
</script>
```
````

---

# Components

<div grid="~ cols-2 gap-4">
<div>

You can use Vue components directly inside your slides.

We have provided a few built-in components like `<Tweet/>` and `<Youtube/>` that you can use directly. And adding your custom components is also super easy.

```html
<Counter :count="10" />
```

<!-- ./components/Counter.vue -->
<Counter :count="10" m="t-4" />

Check out [the guides](https://sli.dev/builtin/components.html) for more.

</div>
<div>

```html
<Tweet id="1390115482657726468" />
```

<Tweet id="1390115482657726468" scale="0.65" />

</div>
</div>

<!--
Presenter note with **bold**, *italic*, and ~~striked~~ text.

Also, HTML elements are valid:
<div class="flex w-full">
  <span style="flex-grow: 1;">Left content</span>
  <span>Right content</span>
</div>
-->

---
class: px-20
---

# Themes

Slidev comes with powerful theming support. Themes can provide styles, layouts, components, or even configurations for tools. Switching between themes by just **one edit** in your frontmatter:

<div grid="~ cols-2 gap-2" m="t-2">

```yaml
---
theme: default
---
```

```yaml
---
theme: seriph
---
```

<img border="rounded" src="https://github.com/slidevjs/themes/blob/main/screenshots/theme-default/01.png?raw=true" alt="">

<img border="rounded" src="https://github.com/slidevjs/themes/blob/main/screenshots/theme-seriph/01.png?raw=true" alt="">

</div>

Read more about [How to use a theme](https://sli.dev/guide/theme-addon#use-theme) and
check out the [Awesome Themes Gallery](https://sli.dev/resources/theme-gallery).

---

# Clicks Animations

You can add `v-click` to elements to add a click animation.

<div v-click>

This shows up when you click the slide:

```html
<div v-click>This shows up when you click the slide.</div>
```

</div>

<br>

<v-click>

The <span v-mark.red="3"><code>v-mark</code> directive</span>
also allows you to add
<span v-mark.circle.orange="4">inline marks</span>
, powered by [Rough Notation](https://roughnotation.com/):

```html
<span v-mark.underline.orange>inline markers</span>
```

</v-click>

<div mt-20 v-click>

[Learn more](https://sli.dev/guide/animations#click-animation)

</div>

---

# Motions

Motion animations are powered by [@vueuse/motion](https://motion.vueuse.org/), triggered by `v-motion` directive.

```html
<div
  v-motion
  :initial="{ x: -80 }"
  :enter="{ x: 0 }"
  :click-3="{ x: 80 }"
  :leave="{ x: 1000 }"
>
  Slidev
</div>
```

<div class="w-60 relative">
  <div class="relative w-40 h-40">
    <img
      v-motion
      :initial="{ x: 800, y: -100, scale: 1.5, rotate: -50 }"
      :enter="final"
      class="absolute inset-0"
      src="https://sli.dev/logo-square.png"
      alt=""
    />
    <img
      v-motion
      :initial="{ y: 500, x: -100, scale: 2 }"
      :enter="final"
      class="absolute inset-0"
      src="https://sli.dev/logo-circle.png"
      alt=""
    />
    <img
      v-motion
      :initial="{ x: 600, y: 400, scale: 2, rotate: 100 }"
      :enter="final"
      class="absolute inset-0"
      src="https://sli.dev/logo-triangle.png"
      alt=""
    />
  </div>

  <div
    class="text-5xl absolute top-14 left-40 text-[#2B90B6] -z-1"
    v-motion
    :initial="{ x: -80, opacity: 0}"
    :enter="{ x: 0, opacity: 1, transition: { delay: 2000, duration: 1000 } }">
    Slidev
  </div>
</div>

<!-- vue script setup scripts can be directly used in markdown, and will only affects current page -->
<script setup lang="ts">
const final = {
  x: 0,
  y: 0,
  rotate: 0,
  scale: 1,
  transition: {
    type: 'spring',
    damping: 10,
    stiffness: 20,
    mass: 2
  }
}
</script>

<div
  v-motion
  :initial="{ x:35, y: 30, opacity: 0}"
  :enter="{ y: 0, opacity: 1, transition: { delay: 3500 } }">

[Learn more](https://sli.dev/guide/animations.html#motion)

</div>

---

# LaTeX

LaTeX is supported out-of-box. Powered by [KaTeX](https://katex.org/).

<div h-3 />

Inline $\sqrt{3x-1}+(1+x)^2$

Block
$$ {1|3|all}
\begin{aligned}
\nabla \cdot \vec{E} &= \frac{\rho}{\varepsilon_0} \\
\nabla \cdot \vec{B} &= 0 \\
\nabla \times \vec{E} &= -\frac{\partial\vec{B}}{\partial t} \\
\nabla \times \vec{B} &= \mu_0\vec{J} + \mu_0\varepsilon_0\frac{\partial\vec{E}}{\partial t}
\end{aligned}
$$

[Learn more](https://sli.dev/features/latex)

---

# Diagrams

You can create diagrams / graphs from textual descriptions, directly in your Markdown.

<div class="grid grid-cols-4 gap-5 pt-4 -mb-6">

```mermaid {scale: 0.5, alt: 'A simple sequence diagram'}
sequenceDiagram
    Alice->John: Hello John, how are you?
    Note over Alice,John: A typical interaction
```

```mermaid {theme: 'neutral', scale: 0.8}
graph TD
B[Text] --> C{Decision}
C -->|One| D[Result 1]
C -->|Two| E[Result 2]
```

```mermaid
mindmap
  root((mindmap))
    Origins
      Long history
      ::icon(fa fa-book)
      Popularisation
        British popular psychology author Tony Buzan
    Research
      On effectiveness<br/>and features
      On Automatic creation
        Uses
            Creative techniques
            Strategic planning
            Argument mapping
    Tools
      Pen and paper
      Mermaid
```

```plantuml {scale: 0.7}
@startuml

package "Some Group" {
  HTTP - [First Component]
  [Another Component]
}

node "Other Groups" {
  FTP - [Second Component]
  [First Component] --> FTP
}

cloud {
  [Example 1]
}

database "MySql" {
  folder "This is my folder" {
    [Folder 3]
  }
  frame "Foo" {
    [Frame 4]
  }
}

[Another Component] --> [Example 1]
[Example 1] --> [Folder 3]
[Folder 3] --> [Frame 4]

@enduml
```

</div>

Learn more: [Mermaid Diagrams](https://sli.dev/features/mermaid) and [PlantUML Diagrams](https://sli.dev/features/plantuml)

---
foo: bar
dragPos:
  square: 691,32,167,_,-16
---

# Draggable Elements

Double-click on the draggable elements to edit their positions.

<br>

###### Directive Usage

```md
<img v-drag="'square'" src="https://sli.dev/logo.png">
```

<br>

###### Component Usage

```md
<v-drag text-3xl>
  <carbon:arrow-up />
  Use the `v-drag` component to have a draggable container!
</v-drag>
```

<v-drag pos="530,234,261,_,-15"undefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefinedundefined>
  <div text-center text-3xl border border-main rounded>
    Double-click me!
  </div>
</v-drag>

<img v-drag="'square'" src="https://sli.dev/logo.png">

###### Draggable Arrow

```md
<v-drag-arrow two-way />
```

<v-drag-arrow pos="67,452,253,46" two-way op70 />

---
src: ./pages/imported-slides.md
hide: false
---

---

# Monaco Editor

Slidev provides built-in Monaco Editor support.

Add `{monaco}` to the code block to turn it into an editor:

```ts {monaco}
import { ref } from 'vue'
import { emptyArray } from './external'

const arr = ref(emptyArray(10))
```

Use `{monaco-run}` to create an editor that can execute the code directly in the slide:

```ts {monaco-run}
import { version } from 'vue'
import { emptyArray, sayHello } from './external'

sayHello()
console.log(`vue ${version}`)
console.log(emptyArray<number>(10).reduce(fib => [...fib, fib.at(-1)! + fib.at(-2)!], [1, 1]))
```

---
layout: center
class: text-center
---

# Learn More

[Documentation](https://sli.dev) · [GitHub](https://github.com/slidevjs/slidev) · [Showcases](https://sli.dev/resources/showcases)

<PoweredBySlidev mt-10 />
