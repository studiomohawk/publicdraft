---
layout: post
title: "Jade as Prototype Language"
date: 2012-12-16 20:20:48
byline: "Yuya Saito"
---

## メッセージ

Node.js用テンプレートエンジンである[Jade]([Jade - Template Engine](http://jade-lang.com/)はHTMLから本当に必要な部分だけを抜き出した言語。そのシンプルながら非常に強力なテンプレートエンジンはモダンウェブ開発に欠かすことが出来ないプロトタイプを作る言語として適している。  
今回はプロトタイプ言語としてのJadeをJadeの基礎を踏まえて紹介していこう。

## アウトライン

### JadeはHamlに影響を受けて生まれた言語

- [Haml](http://haml.info/) / HTML abstraction markup languade
- JadeはそのHamlに影響を受けたJavaScript実装のテンプレート言語
- 例:

~~~jade
!!!
html
  head
    title my jade template
  body
    h1 Hello #{name}
~~~

このJadeに`{"name": "Bob"}`をデータとして渡すと

~~~html
<!DOCTYPE html>
<html>
  <head>
    <title>my jade template</title>
  </head>
  <body>
    <h1>Hello Bob</h1>
  </body>
</html>
~~~

というHTMLに変換される。

- 閉じタグ忘れもなく、`<>`で囲む必要もない
- `#id`を付与するには: `div#id`
- `.class`を付与するには: `div.class`
- 複数クラスは: `div.class1.class2`
- これにIDも付与できる: `div#id.class1.class2`
- `div`を書くのは面倒？: `#id`または`.class`のみで`div`を補完
- テキストは: `p テキスト`
- 複数行に渡るテキストは:

~~~jade
p
  | foo bar baz
  | rawr rawr
  | super cool
  | go jade go
~~~

- コメントもエレガント: `// JavaScriptのコメントと同じ`
- 入れ子になるタグも:

~~~jade
ul
  li.first
    a(href='#') foo
  li
    a(href='#') bar
  li.last
    a(href='#') baz
~~~

- 属性の指定は: `a(href='/login', title='View login page')`
- より詳しい文法については → [Jade Template Syntax Documentation by Example](http://naltatis.github.com/jade-syntax-docs/)でインタラクティブに学ぶことができる。

#### インストール

- `npm install jade`
- グローバルとしてインストールするには: `sudo npm install -g jade`
- [jade.js](https://github.com/visionmedia/jade/blob/master/jade.js)はクライアントサイドでJadeをHTMLに変換できる。
- Jade内でmarkdownを利用するために以下のどれか1つをインストール。(個人的にはmarkedだとGitHubで採用されている形式が利用できるのでおすすめ)
	- markdown.js / `sudo npm install -g markdown`
	- node-discount / `sudo npm install -g discount`
	- marked / `sudo npm install -g marked`

### プロトタイプ言語

- HTMLベースのプロトタイプはデベロッパ向け？
- Jadeはシンプルで強力 === HTMLをより簡単により早く記述できる。
- プロトタイプ言語という提案。
- Jadeをテンプレート言語として使うにはnode.jsが必要。しかし、プロトタイプ専用言語として利用すれば利用シーンが広がる。

#### コンテンツファースト時代のデザイン手法

- Stehen Hay氏は[Responsive Design Workflow](https://speakerdeck.com/stephenhay/responsive-design-workflow)にて:
	- Designing in Text → ワークフローの早い段階でコンテンツとなるテキストをmarkdownを使って構造化のデザインをする手法について紹介している。
- **Filter** in Jade: markdownをJadeのフィルターとして利用できる
- markdownはデザイナ、ディレクタ、デベロッパの共通言語として利用できるほどシンプルで、このケースには十分な機能を有している。
	- 見出し、テキスト、リンク、リストなど

#### 使い回しができるパーツ

- **Includes** in Jade: 

~~~text
./layout.jade
./includes/
  ./head.jade
  ./foot.jade
~~~

上記の様なファイル構造だとして   

~~~jade
html
  include includes/head
  body
    h1 My Site
    p Welcome to my super amazing site.
    include includes/foot
~~~

このようにあらかじめ作成したコンポーネント群を呼び出すことができる。

- JavaScriptやCSSでも同じ傾向にあるようにJadeを使えばHTMLでもモジュール方式の開発ができることになる。
- Jonathan Snooks氏は[SMACSS](https://smacss.com/)でSassを使ったモジュール方式の開発用に以下のようなディレクトリ構成を提案している:

~~~text
./layout/
  ./grid.scss
  ./alternate.scss
./module/
  ./callout.scss
  ./bookmarks.scss
  ./btn.scss
  ./btn-compose.scss
./base.scss
./states.scss
./site-settings.scss
./mixins.scss
~~~

- Jadeを使えば非常に近しい構成を作ることが可能だ:

~~~text
.index.jade
./includes/
  ./system/
    ./head.jade
    ./foot.jade
  ./layout/
    ./grid.jade
  ./module/
    ./btn.jade
    ./nav.jade
~~~

- デザイナが編集を行うのは`index.jade`だけで、デベロッパと協力して必要なモジュールを用意してもらえばいい。
- よく使うパーツがあれば、それを抽象化しモジュールとしてコーディングを行うのはデベロッパに任せればいい。
- CSSプリプロセッサを利用していれば、この構造に合わせてCSSも用意しておけば関係性を明確にできる。

#### ミックスインとテンプレート継承

- Jadeにもミックスインそして、extendsと似たテンプレート継承がある。  
- デベロッパはプロトタイプの作成を柔軟に、高速に行うためにこれらの機能を上手に使ってテンプレートを抽象化することが可能。

### テンプレート言語としてのJadeの使い方

- コマンドラインツールを使ってJadeからHTMLに変換を行う。
- GUI:
	- [LiveReload](http://livereload.com/)
	- [CodeKit](http://incident57.com/codekit/)
- 使うコマンドは1つ: `jade --pretty JADE.FILE` (動画デモ)

### ProtoJadeライブラリ

- プロトタイプ言語としてのJadeのベースとなるオープンソースプロジェクト (作成予定)
- 基本機能(草案):
	- [HTML5 Boilerplate](http://html5boilerplate.com/)をベースに
	- [Twitter Boostrap]にあるUI群をモジュールとして実装
	- [Stylus](http://learnboost.github.com/stylus/)を利用
	- [Components](http://tjholowaychuk.com/post/27984551477/components)ライクな構造とモジュール群の管理、拡張
	- [StyleDocco](http://jacobrask.github.com/styledocco/)を利用してスタイルガイドを作成、プロトタイプライブラリページとする

## リサーチ

- [Jade - Template Engine](http://jade-lang.com/)
- [visionmedia/jade · GitHub](https://github.com/visionmedia/jade#readme)
- [Jade Template Syntax Documentation by Example](http://naltatis.github.com/jade-syntax-docs/)
- [Responsive Design Workflow // Speaker Deck](https://speakerdeck.com/stephenhay/responsive-design-workflow)
- [Scalable and Modular Architecture for CSS](https://smacss.com/)
