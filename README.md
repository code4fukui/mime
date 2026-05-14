# mime

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A comprehensive, compact MIME type module.

[
![Mime CI](https://github.com/broofa/mime/actions/workflows/ci.yml/badge.svg?branch=main)
](https://github.com/broofa/mime/actions/workflows/ci.yml?query=branch%3Amain)
[
![NPM version](https://img.shields.io/npm/v/mime)
](https://www.npmjs.com/package/mime)
[
![NPM downloads](https://img.shields.io/npm/dm/mime)
](https://www.npmjs.com/package/mime)

## Install

```bash
npm install mime
```

## Quick Start

The full version includes 800+ MIME types and 1,000+ extensions.

```javascript
const mime = require('mime');

mime.getType('txt');                    // 'text/plain'
mime.getExtension('text/plain');        // 'txt'
```

## Browser

It is recommended that you use a bundler such as [webpack](https://webpack.github.io/) or [browserify](http://browserify.org/). However, browser-ready versions are available via skypack.dev:

```html
<!-- Full version -->
<script type="module">
  import mime from "https://cdn.skypack.dev/mime";
</script>

<!-- Lite version -->
<script type="module">
  import mime from "https://cdn.skypack.dev/mime/lite";
</script>
```

## Lite Version

The "lite" version of this module omits vendor-specific (`*/vnd.*`) and experimental (`*/x-*`) types. It weighs in at ~2KB, compared to 8KB for the full version.

```javascript
const mime = require('mime/lite');
```

## Mime vs. mime-types vs. mime-db

- **`mime-db`** is the source of truth for MIME type information. It is a canonical dataset of MIME type definitions from IANA, Apache, NGINX, and the Node.js community. It is not an API.
- **`mime-types`** is a thin wrapper around `mime-db` that provides a legacy-compatible API.
- **`mime`** is a self-contained module with a pre-optimized version of the `mime-db` dataset. It provides a simplified API that intelligently resolves type conflicts using [mime-score](https://github.com/broofa/mime-score).

| Module      | Size (minified+compressed) |
|-------------|----------------------------|
| `mime-db`   | 18 KB                      |
| `mime-types`| same as mime-db            |
| `mime`      | 8 KB                       |
| `mime/lite` | 2 KB                       |

## API

Both `require('mime')` and `require('mime/lite')` return instances of the `Mime` class.

Note: Inputs to this API are case-insensitive. Outputs (returned values) will be lowercase.

### `mime.getType(pathOrExtension)`

Get the MIME type for a given path or extension.

```javascript
mime.getType('js');             // 'application/javascript'
mime.getType('dir/text.txt');   // 'text/plain'
mime.getType('dir\\text.txt');  // 'text/plain'
mime.getType('.html');          // 'text/html'
```

Returns `null` if the extension is not detected or recognized.

```javascript
mime.getType('foo/txt');        // null
mime.getType('bogus_type');     // null
```

### `mime.getExtension(type)`

Get the extension for a given MIME type. Charset options (often included in `Content-Type` headers) are ignored.

```javascript
mime.getExtension('text/plain');               // 'txt'
mime.getExtension('application/json');         // 'json'
mime.getExtension('text/html; charset=utf8');  // 'html'
```

### `new Mime(typeMap, ...)`

Create a new `Mime` instance with custom mappings.

```javascript
// Require Mime class
const Mime = require('mime/Mime');

// Define mime type -> extensions map
const typeMap = {
  'text/abc': ['abc', 'alpha'],
  'text/def': ['leppard']
};

// Create and use Mime instance
const myMime = new Mime(typeMap);
myMime.getType('alpha');          // 'text/abc'
myMime.getExtension('text/def');  // 'leppard'
```

### `mime.define(typeMap[, force])`

Define more type mappings.

By default, this method will throw an error if you try to map an extension that is already assigned to another type. Pass `true` for the `force` argument to override previous mappings.

```javascript
mime.define({'text/x-abc': ['abc', 'abcd']});

mime.getType('abcd');            // 'text/x-abc'
mime.getExtension('text/x-abc')  // 'abc'
```

## Command-Line Tool

```bash
$ mime path/to/file.json
application/json

$ mime .html
text/html

$ mime -r application/json
json
```

## License

MIT License