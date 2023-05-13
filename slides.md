---
theme: seriph
class: text-center
highlighter: shiki
lineNumbers: true
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
transition: slide-left
css: unocss
presenter: true
title: Feature driven folder structure
---

# Feature driven folder structure

## 2023春大LT

## 学部 3年 ERASER（加藤 豪）

<div class="pt-12">
  Press Space for next page <carbon:arrow-right class="inline"/>
</div>

<!--
こんにちは  
これはスピーカーノートです
-->

---
---

# ERASER

- 加藤 豪
- 会津大学 学部３年
- [Twitter](https://twitter.com/eraser5th), [GitHub](https://github.com/eraer5th)
- [ポートフォリオ](https://eraser5th.dev)

技術

- 言語：JS・TS、CSS、HTML、Lua、Rust(簡単な競プロ)、Haskell(勉強中)
- Webフロント：React.js、Next.js、GraphQL(Apollo Client)、CSS-in-JS
- バックエンド：Node.js、Prisma、
- ツール：Git・GitHub、Figma

好きなもの

- Neovim、Wezterm、綺麗なコードを考える
- Vtuber、スプラ、EGOIST

<!--
あほい
-->

---

# 今回のお話

みんな大好き、非常に香ばしい「良いコード」について

タイトルの通り、Feature driven folder structureについて(、から脱線しまっくった話)です。

ただし、自分はまだいくつかの記事を読み合わせただけで実際に使った経験があるわけではありません。

ですので、経験に基づいた話というよりかは記事の受け売りという感じが強いです。

が、手探りながらもきちんと扱えるという浅い考えのもとお話しいたします。

<br>

もし適当なことを言っていたりしたら、シメていただけると泣いて喜びます。

---

# Feature driven folder structure ってなんぞ？

機能駆動のフォルダ構造です（翻訳しただけ）。

<br>

これが何を表すか？  
それについて考えていくのが今回のお話です。

ですのでここで結論をネタバレするのはやめておきます。

---
layout: center
---

# まずは技術駆動のフォルダ構造について


---

# こんなReactのコードベース、見たことない？

よくある、かは知らないが、僕がこれまでよくやっていたReactのコードベース

```
src/
├ components/
├ context/
├ hooks/
├ pages/
├ lib/
└ App.ts
```

これが技術駆動のフォルダ構造であり、  
**アンチパターン「技術駆動パッケージング」** の一例です

---

# 技術駆動パッケージングというアンチパターン

これまで無数に踏み抜いて苦しんできた憎きアンチパターン

では技術駆動のどこが良くないのでしょうか？

先程のディレクトリ構成の内の一部を中身が見えるようにしてみましょう。

```
src/
├ components/
  ├ login-form.tsx
  └ ...
├ hooks/
  ├ use-login.tsx
  └ ...
└ pages/
  ├ login-page.tsx
  └ ...
...
```

---

# 技術駆動パッケージングというアンチパターン

何もおかしくはありません。  
ログイン用のコンポーネント・フック・ページそれぞれがその技術的責務に従いそれぞれのフォルダに収まっています。

```
src/
├ components/
  ├ login-form.tsx
  └ ...
├ hooks/
  ├ use-login.tsx
  └ ...
└ pages/
  ├ login-page.tsx
  └ ...
...
```

おかしくはないですがこれに問題はないでしょうか？  
これについて考えるのは後に回してもう一つ見てみましょう

---

# 技術駆動パッケージングというアンチパターン

今度は全体ではなく`components`の中を見てみます。

```
src/
├ components/
  ├ button.tsx
  ├ card.tsx
  ├ login-form.tsx
  ├ user-profile-card.tsx
  └ ...
...
```

今回は簡単と誇張のために`components`内部は全てフラットであるものとしました。

---

# 技術駆動パッケージングというアンチパターン

これまた何もおかしくはありません。  
pages(もしくは他の大きなコンポーネント)から呼び出されるコンポーネントたちがcomponentsの中に収まっているだけです。

```
src/
├ components/
  ├ button.tsx
  ├ card.tsx
  ├ login-form.tsx
  ├ user-profile-card.tsx
  └ ...
...
```

ですがここで、先程後回しにしていた問題についてこれも併せて考えてみます。

---

# 技術駆動パッケージングの問題点

まずはこちらから。

```
src/
├ components/
  ├ login-form.tsx
  └ ...
├ hooks/
  ├ use-login.tsx
  └ ...
└ pages/
  ├ login-page.tsx
  └ ...
...
```

これから読み取れる問題点は、ログインのhookとcomponentとpageがバラバラの場所にあり、把握がしずらいというものです。

---

# 技術駆動パッケージングの問題点

そして次にこちら。

```
src/
├ components/
  ├ button.tsx
  ├ card.tsx
  ├ login-form.tsx
  ├ user-profile-card.tsx
  └ ...
...
```


これから読み取れる問題点は、

- `button`と`login-form`が同じレイヤにいて、`components`の抽象度がバラバラ。
- 用途の全く違う`login-form`と`user-profile-card`が同じパッケージ（ディレクトリ）にいる。

---

# 技術駆動パッケージングの問題点

これらの問題を生み出すものの名前なんというか、我々は知っています。  
凝集度です。

凝集度だと分かれば見えてくるものもあります。  
`components`の凝集度はどれに当たるでしょう?

おそらくは最低最悪の偶発的凝集です。

<span class="color-gray-500">
  凝集度については説明は省きます（一人大LTになってしまう）
</span>

---

# 技術駆動パッケージングの改善

技術駆動パッケージングの問題点が、ある機能の低凝集とそれぞれのディレクトリの偶発的凝集だと分かったので、これを凝集度を元に改善しましょう。

段階的にやるのは面倒なので結論をドン

```
src/
└ features/
  ├ login/
    ├ use-login.ts
    ├ login-page.tsx
    └ login-form.tsx
  ├ ui/
    ├ button.tsx
    ├ card.tsx
    └ ...
  └ ...
...
```

---

# 技術駆動パッケージングの改善

やったことは二つ。

- 偶発的蟻集を起こしていたパッケージcomponents, hooks, pagesを削除
- その中身を機能ごとにパッケージング

```
src/
└ features/
  ├ login/
    ├ use-login.ts
    ├ login-page.tsx
    └ login-form.tsx
  ├ ui/
    ├ button.tsx
    ├ card.tsx
    └ ...
  └ ...
...
```

---

# 技術駆動パッケージングとFDFA


はい。  
もうお気づきでしょうが、これがFeature driven folder architectureになります。

ページに関してはfeaturesの中から出してしまい、以下のようにする場合もあるようです。

```
src/
├ features/
└ pages/
...
```

---

# では機能駆動のフォルダ構造は？

以下が機能駆動で作られたコードベースの一例です。

```
src/
├ features/
  ├ user/
    ├ login-form.tsx
    ├ use-login.tsx
    └ ...
  └ ...
├ pages/
  ├ login.tsx
  ├ article.tsx
  └ ...
└ App.tsx
...
```


---

# fin

ご清聴ありがとうございました！
