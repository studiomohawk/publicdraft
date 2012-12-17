---
layout: post
title: "npm / package.json"
date: 2012-12-16 9:58:27
byline: Yuya Saito
---

## メッセージ

npm(nodeパッケージマネジャ)とそのパッケージのメタデータを保持するpackage.jsonはnode.jsでの開発だけではなく、フロントエンド開発におけるJavaScriptプロジェクトの管理を行うのに必須となりつつあるツール。  

npmとpackage.jsonを使いこなせるようになると[grunt.js](http://gruntjs.com/)や[bower](https://github.com/twitter/bower)、そして[Jam](http://jamjs.org/)などの開発をより加速し、ライブラリ群のマネジメントを楽にするツールもより使いこなせるようになる。

今回は、そのnpmとpackage.jsonについて紹介していこう。

## アウトライン

### npm

#### インストール

- [node.js](http://nodejs.org/download/)提供のインストーラに付属  
- `curl https://npmjs.org/install.sh | sh` ([参考](https://npmjs.org/doc/faq.html#How-do-I-update-npm))

#### npmコマンド

- **`npm install`**: パッケージをインストール

	- 使い方は大きく分けて2つ: 

		- プロジェクトに対してローカルなインストールを行う(こちらがデフォルト) /`npm install <PACKAGE>`
		- コマンドラインツールなど用のインストールを行うグローバルがある /`npm install -g <PACKAGE>`

	- ローカルインストールでは`./node_modules`にモジュールをインストールし、実行ファイルは`./node_modules/.bin/`にインストールされる。(おそらくPATHの設定だと思われるが私の環境ではpackage.jsonファイルがない場合にうまくローカル指定でインストール出来なかった)

	- グローバルインストールでは`{prefix}/lib/node_modules`にモジュールをインストールし、`{prefix}/bin`に実行ファイルをインストール。ついでに`man`ファイルがある場合は`{prefix}/share/man`にインストールを行う。  
`{prefix}`は少なくともMac OS Xでは`/usr/local`というパスになる。

	- またどちらの場合でもバージョンの指定(後述)がない場合は常に公開されている最新のパッケージがインストールされる。

- **`npm search <SEARCH KEYWORD>`**: npmのパッケージを検索

- **`npm ls`**: インストール済みのパッケージの一覧を表示。

- `npm -g ls`とするとシステム全体のパッケージを一覧できる。

- **`npm update`**: インストール済みのパッケージをアップデート。

	- インストール済みのパッケージの最新バージョンを検索
	- もしあればインストールする
	- 依存設定が問題なければ依存されているパッケージを最新バージョンに差し替える
	- 古いバージョンが依存設定されていない場合に古いバージョンのパッケージを削除する

- **`npm outdated`**: package.jsonファイルで指定している依存パッケージがアップデートされているかを確認する。

#### npmの自動補完

- `. <(npm completion)`を`~/.bashrc`なり `~/.zshrc`なりに記述すれば`tab`でコマンドを自動補完できる。

### package.json

- package.jsonファイルはプロジェクトにおける様々なメタデータを保持するファイル。  

~~~text
{
  "name": "best-practices",
  "description": "A package using versioning best-practices",
  "version": "0.0.1",
  "dependencies": {
    "colors": "0.x.x",
    "express": "2.3.x",
    "optimist": "0.2.x"
  },
  "devDependencies": {
    "mocha": "1.7.x"
  },
  "engine": "node >= 0.4.1"
}
~~~

- npmパッケージとして公開する場合には[こちらを参照](https://npmjs.org/doc/json.html)
- `name`と`version`は必須
- 開発用ツールとしてnpmを利用する場合は`dependencies`よりも`devDependencies`に利用するパッケージを記載するほうがいい。  
ユニットテスト用のツールやgrunt、gruntのプラグインなどは`devDependencies`に記載する。

#### バージョンの指定:

- 上記の例では:`"mocha": "1.7.x"`の`"1.7.x"`でパッケージのバージョンを指定。  
ここでは[mocha](http://visionmedia.github.com/mocha/)のバージョン1.7.系のバージョンを指定している。1.7の範囲内で最新を取得してくるという意味。

- package.jsonで指定できるパッケージは`npm install`コマンドでインストールできるパッケージと同等。

- **レンジの指定**

	- 例えば`v1.5`から`v1.9`までで最新を取得する。という様な指定を行うのがレンジ。
	- npmでは[node-semver](https://github.com/isaacs/node-semver)を使ってバージョンの管理を行っているそう。  

	- `>1.2.3` / `1.2.3`よりも新しいバージョンを取得
	- `<1.2.3` / `1.2.3`よりも古いバージョンを取得
	- `~1.2.3` / 最低でも`1.2.3`バージョン、最新でも`1.3.0`未満を取得
	- `1.2.x` / `1.2.0`から`1.3.0`未満の最新バージョンを取得
	- `*` / 常に最新バージョンを取得

- **インストール元の指定**

	- デフォルトではnpm registryに公開されたパッケージを取得するが、それ以外のインストール元を指定することも可能。

	- `git://github.com/user/repo.git#ref` 
		- GitHubで **公開** されているレポジトリをインストールできる。
		- `#ref`はオプションでブランチ名やタグ名、コミットIDを指定できる。

~~~text
dependencies": {
    "public": "git://github.com/user/repo.git#ref", 
		"private": "git+ssh://git@github.com:user/repo.git#ref"
}
~~~

	- package.jsonファイルでは上記の様に記述。(ちなみに`"private"`の値はGitHubでプライベートとなっているレポジトリの指定の例)

## リサーチ

### npm

- [npm documentation](https://npmjs.org/doc/)
- [npm 1.0: Global vs Local installation](http://blog.nodejs.org/2011/03/23/npm-1-0-global-vs-local-installation/)
- [npm](https://npmjs.org/)
- [Introduction to npm - How To Node - NodeJS](http://howtonode.org/introduction-to-npm)
- [Semantic Versioning 2.0.0-rc.1](http://semver.org/)

### package.json

- [Package.json: an interactive guide](http://package.json.jit.su/)
- [What is the file package.json?](http://docs.nodejitsu.com/articles/getting-started/npm/what-is-the-file-package-json)
- [Package.json dependencies done right](http://blog.nodejitsu.com/package-dependencies-done-right)




