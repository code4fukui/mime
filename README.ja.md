# Mime

包括的で簡潔なMIMEタイプモジュール。

## インストール

### NPM
```
npm install mime
```

### ブラウザ

[webpack](https://webpack.github.io/)や[browserify](http://browserify.org/)などのバンドラーを使うことをおすすめします。
ただし、以下のようにskypack.devからも利用できます:

```
// 完全版
<script type="module">
import mime from "https://cdn.skypack.dev/mime";
</script>
```

```
// "ライト"版
<script type="module">
import mime from "https://cdn.skypack.dev/mime/lite";
</script>
```

## クイックスタート

完全版(800種類以上のMIMEタイプ、1,000種類以上の拡張子)の使い方:

```javascript
const mime = require('mime');

mime.getType('txt');                    // ⇨ 'text/plain'
mime.getExtension('text/plain');        // ⇨ 'txt'
```

APIの詳細は[MimeクラスのAPI](#mimeクラスのapi)を参照してください。

## ライト版

このモジュールの"ライト"版では、ベンダー固有(`*/vnd.*`)や試験用(`*/x-*`)のタイプを省略しています。
完全版の約2.5KBと比べて容量は小さくなっています。ライト版を使うには以下のようにします:

```javascript
const mime = require('mime/lite');
```

## Mime、mime-types、mime-dbモジュールの違い

これらの人気NPMモジュールの違いは以下の通りです:

[`mime-db`](https://github.com/jshttp/mime-db)はMIMEタイプ情報の「真実の源」です。APIではなく、IANAやApache、NGINX、Nodeコミュニティから提出された拡張マッピングなどの正規データセットです。

[`mime-types`](https://github.com/jshttp/mime-types)は`mime @ < v1.3.6`のAPIと互換性のある(ほぼ)薄いラッパーです。

`mime`は現在のバージョン2以降、最適化された`mime-db`データセットをバンドルした自己完結型のモジュールです。以下の特徴を持ちます:

- 競合するタイプを賢明に解決 (詳細は[mime-score](https://github.com/broofa/mime-score)を参照)
- 業界のベストプラクティスに沿ったメソッド命名
- コンパクトなフットプリント。各モジュールの圧縮サイズの比較:

モジュール | サイズ
--- | ---
`mime-db`  | 18 KB
`mime-types` | mime-dbと同等
`mime` | 8 KB
`mime/lite` | 2 KB

## MimeクラスのAPI

`require('mime')`と`require('mime/lite')`はともにMIMEクラスのインスタンスを返します。以下に詳細を記します。

注: このAPIの入力は大文字小文字を区別しません。返り値は小文字になります。

### new Mime(typeMap, ...追加のマップ)

ほとんどのユーザーは直接Mimeインスタンスを作成する必要はありませんが、カスタムマッピングを作りたい場合は以下のように行えます:

```javascript
// Mimeクラスを読み込む
const Mime = require('mime/Mime');

// MIMEタイプ -> 拡張子のマップを定義
const typeMap = {
  'text/abc': ['abc', 'alpha', 'bet'],
  'text/def': ['leppard']
};

// Mimeインスタンスを作成して使用
const myMime = new Mime(typeMap);
myMime.getType('abc');            // ⇨ 'text/abc'
myMime.getExtension('text/def');  // ⇨ 'leppard'
```

2つ以上のマップ引数が渡された場合、それぞれが順番に`define()`されます(下記参照)。

### mime.getType(pathOrExtension)

指定したパスまたは拡張子のMIMEタイプを取得します。例:

```javascript
mime.getType('js');             // ⇨ 'application/javascript'
mime.getType('json');           // ⇨ 'application/json'

mime.getType('txt');            // ⇨ 'text/plain'
mime.getType('dir/text.txt');   // ⇨ 'text/plain'
mime.getType('dir\\text.txt');  // ⇨ 'text/plain'
mime.getType('.text.txt');      // ⇨ 'text/plain'
mime.getType('.txt');           // ⇨ 'text/plain'
```

拡張子が検出されないか認識されない場合は`null`が返されます:

```javascript
mime.getType('foo/txt');        // ⇨ null
mime.getType('bogus_type');     // ⇨ null
```

### mime.getExtension(type)
指定したMIMEタイプの拡張子を取得します。Content-Typeヘッダーに含まれるcharset オプションは無視されます。

```javascript
mime.getExtension('text/plain');               // ⇨ 'txt'
mime.getExtension('application/json');         // ⇨ 'json'
mime.getExtension('text/html; charset=utf8');  // ⇨ 'html'
```

### mime.define(typeMap[, force = false])

[さらに]タイプマッピングを定義します。

`typeMap`は上記の`new Mime`で文書化したタイプ -> 拡張子のマップです。

デフォルトでは、すでに別のタイプに割り当てられている拡張子にタイプをマッピングしようとするとエラーが発生します。
`force`引数に`true`を渡すと、この動作を抑制して(以前のマッピングを上書きして)拡張子を新しいタイプにマッピングできます。

```javascript
mime.define({'text/x-abc': ['abc', 'abcd']});

mime.getType('abcd');            // ⇨ 'text/x-abc'
mime.getExtension('text/x-abc')  // ⇨ 'abc'
```

## コマンドラインツール

    mime [path_or_extension]

例:

    > mime scripts/jquery.js
    application/javascript