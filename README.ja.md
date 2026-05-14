# mime

包括的でコンパクトなMIMEタイプモジュールです。

[
![Mime CI](https://github.com/broofa/mime/actions/workflows/ci.yml/badge.svg?branch=main)
](https://github.com/broofa/mime/actions/workflows/ci.yml?query=branch%3Amain)
[
![NPM version](https://img.shields.io/npm/v/mime)
](https://www.npmjs.com/package/mime)
[
![NPM downloads](https://img.shields.io/npm/dm/mime)
](https://www.npmjs.com/package/mime)

## インストール

```bash
npm install mime
```

## クイックスタート

フルバージョンには800以上のMIMEタイプと1,000以上の拡張子が含まれています。

```javascript
const mime = require('mime');

mime.getType('txt');                    // 'text/plain'
mime.getExtension('text/plain');        // 'txt'
```

## ブラウザ

[webpack](https://webpack.github.io/)や[browserify](http://browserify.org/)などのバンドラーを使用することをお勧めします。ただし、ブラウザですぐに使えるバージョンもskypack.dev経由で利用可能です。

```html
<!-- フルバージョン -->
<script type="module">
  import mime from "https://cdn.skypack.dev/mime";
</script>

<!-- ライトバージョン -->
<script type="module">
  import mime from "https://cdn.skypack.dev/mime/lite";
</script>
```

## ライトバージョン

このモジュールの「ライト」バージョンでは、ベンダー固有のタイプ（`*/vnd.*`）や実験的なタイプ（`*/x-*`）が省略されています。フルバージョンが8KBであるのに対し、約2KBのサイズに収まっています。

```javascript
const mime = require('mime/lite');
```

## Mime vs. mime-types vs. mime-db

- **`mime-db`** はMIMEタイプ情報の「信頼できる情報源（source of truth）」です。IANA、Apache、NGINX、およびNode.jsコミュニティから収集されたMIMEタイプ定義の正規データセットです。APIではありません。
- **`mime-types`** は `mime-db` の薄いラッパーであり、レガシー互換のAPIを提供します。
- **`mime`** は、事前に最適化された `mime-db` のデータセットを内包する自己完結型モジュールです。[mime-score](https://github.com/broofa/mime-score) を使用してタイプの競合をインテリジェントに解決する、シンプルなAPIを提供します。

| モジュール      | サイズ（最小化＋圧縮） |
|-------------|----------------------------|
| `mime-db`   | 18 KB                      |
| `mime-types`| mime-dbと同じ              |
| `mime`      | 8 KB                       |
| `mime/lite` | 2 KB                       |

## API

`require('mime')` と `require('mime/lite')` は、どちらも `Mime` クラスのインスタンスを返します。

注意: このAPIへの入力は大文字・小文字を区別しません（case-insensitive）。出力（戻り値）は小文字になります。

### `mime.getType(pathOrExtension)`

指定されたパスまたは拡張子のMIMEタイプを取得します。

```javascript
mime.getType('js');             // 'application/javascript'
mime.getType('dir/text.txt');   // 'text/plain'
mime.getType('dir\\text.txt');  // 'text/plain'
mime.getType('.html');          // 'text/html'
```

拡張子が検出または認識されない場合は `null` を返します。

```javascript
mime.getType('foo/txt');        // null
mime.getType('bogus_type');     // null
```

### `mime.getExtension(type)`

指定されたMIMEタイプの拡張子を取得します。文字セットオプション（`Content-Type` ヘッダーによく含まれます）は無視されます。

```javascript
mime.getExtension('text/plain');               // 'txt'
mime.getExtension('application/json');         // 'json'
mime.getExtension('text/html; charset=utf8');  // 'html'
```

### `new Mime(typeMap, ...)`

カスタムマッピングを持つ新しい `Mime` インスタンスを作成します。

```javascript
// Mimeクラスを読み込む
const Mime = require('mime/Mime');

// MIMEタイプ -> 拡張子のマップを定義する
const typeMap = {
  'text/abc': ['abc', 'alpha'],
  'text/def': ['leppard']
};

// Mimeインスタンスを作成して使用する
const myMime = new Mime(typeMap);
myMime.getType('alpha');          // 'text/abc'
myMime.getExtension('text/def');  // 'leppard'
```

### `mime.define(typeMap[, force])`

さらにタイプマッピングを定義します。

デフォルトでは、すでに別のタイプに割り当てられている拡張子をマッピングしようとすると、このメソッドはエラーをスローします。以前のマッピングを上書きするには、`force` 引数に `true` を渡します。

```javascript
mime.define({'text/x-abc': ['abc', 'abcd']});

mime.getType('abcd');            // 'text/x-abc'
mime.getExtension('text/x-abc')  // 'abc'
```

## コマンドラインツール

```bash
$ mime path/to/file.json
application/json

$ mime .html
text/html

$ mime -r application/json
json
```

## ライセンス

MIT License
