---
theme: dracula
class: text-center
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
presenter: true
title: Feature driven folder structureは何を解決する？
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
- [Twitter](https://twitter.com/eraser5th), [GitHub](https://github.com/eraser5th)
- [ポートフォリオ](https://eraser5th.dev)

技術

- 言語：JS・TS、CSS、HTML、Lua、Rust(簡単な競プロ)、Haskell(勉強中)
- Webフロント：React.js、Next.js、GraphQL(Apollo Client)、CSS-in-JS
- バックエンド：Node.js、Prisma(ORM)、
- ツール：Git・GitHub、Figma

好きなもの

- Neovim、Wezterm、綺麗なコードを考える
- Vtuber、スプラ、EGOIST

<img src="/Slack_Icon_20220507_circle.png" />

<!--
あほい
-->

<style>
img {
  position: absolute;
  top: 48px;
  right: 48px;
  width: 140px;
  height: 140px;
}
</style>

---

# 前置き

<br>

今回のお話はマサカリの飛びやすい分野だと思います。

もし適当なことを言っていたりしたら、シメていただけると泣いて喜びます。

---

# Feature driven folder structure ってなんぞ？

<br>

機能駆動のフォルダ構造です（翻訳しただけ）。

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

# Feature driven folder structureは何を解決する？

<br>

これが何を解決するのか？  
それについて考えていくのが今回のお話です。

---

# こんなReactのコードベース、見たことない？

<br>

```
src/
├ components/
├ context/
├ hooks/
├ pages/
├ lib/
└ App.ts
```

よくある、かは知らないが、僕がこれまでよくやっていたReactのコードベース

---

# こんなReactのコードベース、見たことない？

<br>

```
src/
├ components/
├ context/
├ hooks/
├ pages/
├ lib/
└ App.ts
```

これは**アンチパターン「技術駆動パッケージング」** の一例です

<!--
バックエンドなどであれば、レイヤー度アーキテクチャなどでレイヤーごとのフォルダをつくるようなものを指します。
-->

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

<style>
code {
  background: rgb(22, 23, 29);
}
</style>

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

- ログインのフックとコンポーネントとページがバラバラの場所にあり、把握がしずらい

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

- `button`と`login-form`が同じレイヤにいるなど、`components`の抽象度がバラバラ
- 用途の全く違う`login-form`と`user-profile-card`が同じパッケージ（ディレクトリ）にいる

<!--
ahoy
あほい
-->

<style>
code {
  background: rgb(22, 23, 29) !important;
}
</style>

---

# 技術駆動パッケージングの問題点

<br>

これらの問題を生み出すものの名前なんというか、我々は知っています。

**凝集度**です。

となると`components`の凝集度はどれに当たるでしょう?  
おそらくは最低最悪の偶発的凝集です。

<span class="color-gray-500">
  凝集度については説明は省きます（一人大LTになってしまう）
</span>

<!--
ahoy
あほい
-->

<style>
code {
  background: rgb(22, 23, 29) !important;
}
</style>

---

# 技術駆動パッケージングの改善

<br>

技術駆動パッケージングの問題点が、  
ある機能の低凝集と、それぞれのディレクトリ内部の偶発的凝集だと分かったので  
これらを機能的凝集へと改善しましょう

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

<!--
そしてこれこそがFeature driven folder structureそのものです！
-->

---
layout: center
---

# Re:

# Feature driven folder structure は何を解決する？

<!--
FDFSが何を解決するのか。  
それは凝集度を解決した結果FDFSになったんだから当然凝集度です。(たぶん)

FDFSというものを使うことにより技術駆動パッケージングを避け、偶発的凝集の誘発を避けます。

（他にもあるのかもしれないけど、それについては知らないです）
-->

---
layout: center
---

# 最後に

---
layout: center
---

# 「銀の弾などない」（戒め）

<!--
今回はFDFSが何を解決するのかということについてお話ししてきました。  
そして、これまで技術駆動パッケージングを行っていたものについてはこれを使うことによってかなりの改善が認められるでしょう。  

しかし、これは決して銀の玉などではなく、適用するかどうか、方法などについては逐次考えていく必要があるということ  
ゆめゆめ忘れないようにしていきましょう！
-->

---
layout: center
---

# fin

ご清聴ありがとうございました！

---

# 参考記事

[Screaming Architecture - Evolution of a React folder structure](https://dev.to/profydev/screaming-architecture-evolution-of-a-react-folder-structure-4g25)

[Screaming Architecture](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
