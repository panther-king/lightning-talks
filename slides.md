---
# You can also start simply with 'default'
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: ニッチな技術の探訪記
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

# ニッチな技術の探訪記



<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    迷わず行けよ、行けば分かるさ <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: two-cols-header
transition: slide-up
---

# 突然のクイズ

アイスブレイクというやつかもしれない

::left::

<v-click>

## 2025年は何の年？

- 令和七年？
- 万博開催？

</v-click>

::right::

<v-click>

## Linuxデスクトップ元年

</v-click>

---
transition: slide-up
---

| 西暦 | 和暦 | 界隈 |
| ---- | ---- | ---- |
| 2018年 | 平成三十年 | Linuxデスクトップ元年 |
| 2019年 | 令和元年 | Linuxデスクトップ元年 |
| 2020年 | 令和二年 | Linuxデスクトップ元年 |
| 2021年 | 令和三年 | Linuxデスクトップ元年 |
| 2022年 | 令和四年 | Linuxデスクトップ元年 |
| 2023年 | 令和五年 | Linuxデスクトップ元年 |
| 2024年 | 令和六年 | Linuxデスクトップ元年 |
| 2025年 | 令和七年 | Linuxデスクトップ元年 |

---
layout: two-cols-header
transition: slide-up
---

# Linuxデスクトップの普及状況

<v-click>

控え目に言って大流行

</v-click>

::left::

<v-click>

<img src="/linux-share-graph.png">
<small>出典: <a href="https://www.qbook.jp/column/1593.html">Qbook【2025年3月版】OSのシェア率ランキング)</a></small>

</v-click>

::right::

<v-click>

- XboxやPlaystationの100倍近いシェア
- Mac OSと比べても約2倍
- このまま行けば2100年頃にはシェア100%

</v-click>

---
transition: slide-up
layout: center
---

閑話休題

---
transition: slide-up
---

# Linuxデスクトップとは？

主要OSとの比較

<v-click>

## Linuxはあくまで __カーネル__

できる事はハードウェアとの橋渡しやプロセス管理なので、そのままではCLIも便利なGUIも存在しない。

これに主要なソフトウェアを同梱して、便利に使えるようにしたものが __ディストリビューション__ 。<br>
(Ubuntu, RHEL, Amazon Linux, Alpine Linux, ...etc)

Linuxカーネルに [GNOME](https://www.gnome.org/) や [KDE](https://kde.org/ja/) といった
「デスクトップ環境(DE)」ソフトウェアを __組み合わせる__ ことで、他OS同様にデスクトップPCとしての利便性を実現している。

ユーザーに自由な選択肢が多数あるので、 ~~ぼくのかんがえたさいきょうのLinuxデスクトップ~~ ニーズに応じた環境を構築できる。

</v-click>

---
transition: slide-up
---

# Linuxデスクトップとは？

主要OSとの比較

<v-click>

## Windows/macOSは __GUIありき__ のアーキテクチャ

カーネルとGUIが __密結合__ といえる状態で強く結びついている。

ベンダーが提供するGUI以外の選択肢が実質無いため、細かいことを考えなくて良い反面、ユーザーの自由度が限定的。

そんな中でもGUIをカスタマイズするソフトウェアは存在するが、あくまでベンダーの箱庭にとどまる前提となっている。

</v-click>

---
transition: slide-up
---

# Linuxデスクトップとは？

主要OSとの比較

<v-click>

| OS | カーネル | GUI | 結合度 |
| ---- | ---- | ---- | ---- |
| Windows | NT Kernel | explorer.exe | 密結合 |
| macOS | Darwin | Finder | 密結合 |
| Linux | Linux | 自由(何なら無くても良い) | 疎結合 |

<small>~~アプリケーションで疎結合なアーキテクチャを意識するなら、利用するOSも当然疎結合だよね？~~</small>

</v-click>

---
layout: two-cols-header
transition: slide-up
---

# Linuxの主要デスクトップ環境

多くのディストリビューションで採用

::left::

## GNOME

<img src="/gnome.png" width="90%">

- macOSライクフィールなGUI
- Ubuntu, RHELなどのデフォルト


::right::

## KDE

<img src="/kde.png" width="90%">

- WindowsライクフィールなGUI
- openSUSEのデフォルト

---
layout: center
transition: slide-up
---

ちょっと待って？「ニッチな技術の探訪記」だよね？

---
transition: slide-up
---

# タイル型ウインドウマネージャー

何それおいしいの？

## GNOMEやKDEが提供するもの

デスクトップ利用に必要なもの全部入り

- ウインドウマネージャー
  - 配置やサイズ変更・移動・装飾など
- ファイルマネージャー
  - アプリケーションとしてのExplorer/Finderに相当
- タスクバー
- アプリケーションランチャー
- デスクトップアイコン
- 標準的なアプリケーション(テキストエディタ、電卓など)

---
transition: slide-up
---

# タイル型ウインドウマネージャー

たった1つの責務(SRP)

- ウインドウをタイルのように配置できます
- (大事な事なのでもう一度)ウインドウをタイルのように配置できます

---
transition: slide-up
---

<Youtube id="RQcBqV_GBAw" width="100%" height="100%" />

---
transition: slide-up
---

# タイル型ウインドウマネージャー

たった1つの事をうまくやる

<v-click>

## ウインドウの管理

- ウインドウを表示するワークスペースの管理
- アプリケーションのウインドウを適切に配置
- 最大化やリサイズ・フローティングなどを切り替え

</v-click>

<v-click>

## 何が嬉しいの？

- ほぼ全ての操作がキーボードだけで完結する
- ウインドウの調整にわずらわされる事が無い
- 好きなソフトウェアを組み合わせて利用できる

</v-click>

---
transition: slide-up
---

# タイル型ウインドウマネージャー

いっぱいあるよ

<v-click>

- X11
  - [awesome](https://awesomewm.org/)
  - [i3](https://i3wm.org/)
  - [Xmonad](https://xmonad.org/)
- Wayland
  - [Hyprland](https://hyprland.org/)
  - [Sway](https://swaywm.org/)

</v-click>

---
transition: slide-up
---

# Linuxデスクトップとは？

いっぱいあるだけが能じゃない

<v-click>

- 選択肢が無数にある
  - 好きなものを組み合わせて、自分のニーズに合った環境を構築できる
  - __選ぶ__ という重要な行為の主導権が、常にユーザー側にある
- コンピューターの根源的な仕組みが学べる
  - OSってそもそも何だろう？どうやって動いてるんだろう？
  - 自分のやりたい事はどうやれば実現できるんだろう？
- 純粋に楽しい
  - 縦に掘れば、自分の主戦場を支えるレイヤーの知識が身につく
  - 横に広げれば、異なる思想や文化・哲学に触れられる

</v-click>
