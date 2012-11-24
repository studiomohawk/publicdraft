---
layout: post
title: "Grunt: A JavaScript command line build tool"
date: 2012-05-20 21:14:24
byline: "Yuya Saito"
category: research
---

Grunt: JavaScript用タスクベースのコマンドラインビルドツール。  

- `concat` / ファイルを結合
- `init` / プロジェクトのscaffoldingをテンプレートから作成
- `lint` / [JSHint](http://www.jshint.com/)を利用したバリデーション
- `min` / [UglifyJS](https://github.com/mishoo/UglifyJS/)による圧縮
- `qunit` / [QUnit](http://docs.jquery.com/QUnit)によるテストをヘッドレス[PhantomJS](http://phantomjs.org/)を利用して実行
- `server` / ウェブサーバを立ち上げる
- `test` / [nodeunit](https://github.com/caolan/nodeunit)を利用したユニットテストを実行
- `watch` / 監視対象のファイルに変更があった場合にタスクを実行する

[github](https://github.com/cowboy/grunt)

## インストール

`npm install -g grunt`  
`qunit`タスク用にPhantomJSが必要になるがnpmからはインストールができないため、
`brew install phantomjs`と`brew`でインストールする。

## 使い方

gruntは実行したディレクトリ内の`grunt.js`というファイルを検索、なければ親ディレクトリと見つけるまで継続して検索し続ける。  
`grunt.js`にはプロジェクトの設定、呼び出すgruntのプラグインやタスクフォルダの設定、タスク、ヘルパそのものを記述する。

**ベーシックなgrunt.jsファイルの例**
{% highlight js %}
module.exports = function(grunt) {

  // プロジェクトの設定
  grunt.initConfig({
    lint: {
      all: ['grunt.js', 'lib/**/*.js''test/**/*.js']
    },
    jshint: {
      options: {
        browser: true
      }
    }
  });

  // 呼び出すgruntのプラグインやタスクフォルダの設定
  grunt.loadNpmTasks('grunt-sample');

  // デフォルトタスク
  grunt.registerTask('default', 'lint sample');

};
{% endhighlight %}

[あらかじめ設定されているjQueryプロジェクトのgrunt.js例](https://github.com/cowboy/grunt-jquery-example/blob/master/grunt.js)

## プラグイン

- [grunt-contrib](https://github.com/gruntjs/grunt-contrib)

gruntによく使うタスクを追加する。

- `clean` / ファイル、フォルダを削除
- `coffee` / CoffeeScriptをJavaScriptにコンパイル
- `handlebars` / handlebarsのテンプレートをコンパイル
- `jade` / jadeテンプレートをHTMLにコンパイル
- `jst` / underscoreのテンプレートをコンパイル
- `less` / lessファイルをCSSにコンパイル
- `mincss` / CSSを圧縮
- `stylus` / StylusファイルをCSSにコンパイル
- `zip` / ファイル、フォルダをzip圧縮

インストール: `npm install grunt-contrib`  
タスクのロード: `grunt.loadNpmTasks('grunt-contrib');`

- [backbone-boilerplate/grunt-bbb](https://github.com/backbone-boilerplate/grunt-bbb)

Backbone Boilerplateフレームワークツール。

インストール: `npm install -g bbb`  
使い方: `bbb init`

- [grunt-css](https://github.com/jzaefferer/grunt-css)

`cssmin`、`csslint`という2つのタスクを追加できる。

インストール: `npm install grunt-css`  
タスクのロード: `grunt.loadNpmTasks('grunt-css');`

- [grunt-recess](https://github.com/sindresorhus/grunt-recess)

Twitterが採用しているスタイルガイドチェッカ[recess](https://github.com/twitter/recess)を利用出来る。

インストール: `npm install grunt-recess`  
タスクのロード: `grunt.loadNpmTasks('grunt-recess');`

**lint、LESSのコンパイル、結合タスクの例**
{% highlight js %}
recess: {
    dist: {
        src: [
            'src/main.css',
            'src/component.css'
        ],
        dest: 'dist/combined.css',
        options: {
            compile: true
        }
    }
}
{% endhighlight %}

- [grunt-requirejs](https://github.com/asciidisco/grunt-requirejs)

[Require.js](http://requirejs.org/)をgruntでビルドするタスクを追加。`r.js`オプティマイザーの利用も可。

インストール: `npm install grunt-requirejs`  
タスクのロード: `grunt.loadNpmTasks('grunt-requirejs');`

{% highlight js %}
requirejs: {
  dir: 'build',
  appDir: 'src',
  baseUrl: 'js',
  paths: {
      underscore: '../vendor/underscore',
      jquery    : '../vendor/jquery',
      backbone  : '../vendor/backbone'
  },
  pragmas: {
      doExclude: true
  },
  skipModuleInsertion: false,
  optimizeAllPluginResources: true,
  findNestedDependencies: true
}
{% endhighlight %}

- [grunt-less](https://github.com/jharding/grunt-less)

LESSをCSSにコンパイル。

インストール: `npm install grunt-less`  
タスクのロード: `grunt.loadNpmTasks('grunt-less');`

- [grunt-exec](https://github.com/jharding/grunt-exec)

Gruntからシェルコマンドを実行。

インストール: `npm install grunt-exec`  
タスクのロード: `grunt.loadNpmTasks('grunt-exec');`

{% highlight js %}
grunt.initConfig({
    exec: {
        remove_logs: {
            command: 'rm -f *.log'
        }
        list_files: {
            command: 'ls -l **',
            stdout: true
        }
    }
});
{% endhighlight %}

- [grunt-reload](https://github.com/webxl/grunt-reload)

gruntnのデフォルト`watch`タスクで指定したファイルの変更に合わせてブラウザのリロードを行う。

インストール: `npm install grunt-reload`
タスクのロード: `grunt.loadNpmTasks('grunt-reload');`

- [grunt-s3](https://github.com/pifantastic/grunt-s3)

Amazon S3 アップロード/ダウンロード。

インストール: `npm install grunt-s3`

{% highlight js %}
grunt.initConfig({

  s3: {
    key: 'YOUR KEY',
    secret: 'YOUR SECRET',
    bucket: 'my-bucket',
    access: 'public-read',

    // Files to be uploaded.
    upload: [
      {
        src: 'important_document.txt',
        dest: 'documents/important.txt',
        gzip: true
      },
      {
        src: 'passwords.txt',
        dest: 'documents/ignore.txt',

        // These values will override the above settings.
        bucket: 'some-specific-bucket',
        access: 'authenticated-read'
      },
      {
        // Wildcards are valid *for uploads only* until I figure out a good implementation
        // for downloads.
        src: 'documents/*.txt',

        // But if you use wildcards, make sure your destination is a directory.
        dest: 'documents/'
      }
    ],

    // Files to be downloaded.
    download: [
      {
        src: 'documents/important.txt',
        dest: 'important_document_download.txt'
      },
      {
        src: 'garbage/IGNORE.txt',
        dest: 'passwords_download.txt'
      }
    ],

    del: [
      {
        src: 'documents/launch_codes.txt'
      },
      {
        src: 'documents/backup_plan.txt'
      }
    ]
  }
});
{% endhighlight %}
