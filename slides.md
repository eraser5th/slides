---
theme: seriph
class: text-center
highlighter: shiki
lineNumbers: false
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

# Feature driven folder structureは何を解決する？

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

タイトルの通り、Feature driven folder structureについてです。

ただし、自分はまだいくつかの記事を読み合わせただけで実際に使った経験があるわけではありません。

ですので、経験に基づいた話というよりかは記事の受け売りという感じが強いです。

が、手探りながらもきちんと扱えるという浅い考えのもとお話しいたします。

<br>

もし適当なことを言っていたりしたら、シメていただけると泣いて喜びます。


---

# Feature driven folder structure ってなんぞ？

<br>

機能駆動のフォルダ構造です（翻訳しただけ）。

これが何を解決するのか？  
それについて考えていくのが今回のお話です。

ですのでここで結論をネタバレするのはやめておきます。

---

# こんなReactのコードベース、見たことない？

<br>

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

---

# こんなReactのコードベース、見たことない？

<br>

**アンチパターン「技術駆動パッケージング」** の一例です

```
src/
├ components/
├ context/
├ hooks/
├ pages/
├ lib/
└ App.ts
```

---
layout: center
---

# 技術駆動パッケージングというアンチパターン

---

# 技術駆動パッケージングというアンチパターン

<br>

技術駆動のどこが良くないのでしょうか？

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

<!--
何もおかしくはありません。  
ログイン用のコンポーネント・フック・ページそれぞれがその技術的責務に従いそれぞれのフォルダに収まっています。

おかしくはありませんが問題はないでしょうか？
一旦それについては置いておいて次も見てみましょう。
-->

---

# 技術駆動パッケージングというアンチパターン

<br>

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

<!--
これまた何もおかしくはありません。  
pages(もしくは他の大きなコンポーネント)から呼び出されるコンポーネントたちがcomponentsの中に収まっているだけです。

ですがここで、先程後回しにしていた問題についてこれも併せて考えてみます。
-->

---

# 技術駆動パッケージングの問題点

<br>

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

- ログインのhookとcomponentとpageがバラバラの場所にあり、把握がしずらい

---

# 技術駆動パッケージングの問題点

<br>

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

- `button`と`login-form`が同じレイヤにいて、`components`の抽象度がバラバラ。
- 用途の全く違う`login-form`と`user-profile-card`が同じパッケージ（ディレクトリ）にいる。

---

# 技術駆動パッケージングの問題点

<br>

これらの問題を生み出すものの名前なんというか、我々は知っています。  
凝集度です。

凝集度の問題だと分かれば見えてくるものもあります。  
`components`の凝集度はどれに当たるでしょう?

おそらくは最低最悪の偶発的凝集です。

<span class="color-gray-500">
  凝集度については説明は省きます（一人大LTになってしまう）
</span>

---

# 技術駆動パッケージングの改善

<br>

技術駆動パッケージングの問題点が  
ある機能の低凝集とそれぞれのディレクトリの偶発的凝集だと分かったので  
機能的凝集へと改善しましょう

---

# 技術駆動パッケージングの改善

<br>

段階的にやるのは面倒なので結果をドン!

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

<br>

やったことは二つ。

- 偶発的凝集を起こしていたパッケージ、components・hooks・pagesを削除
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
layout: center
---

# Re:
# Feature driven folder structure は何を解決する？

---
layout: center
---

# 最後に

## 「銀の弾などない」（戒め）

---
layout: center
---

# fin

ご清聴ありがとうございました！
