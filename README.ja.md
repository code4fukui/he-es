# he

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

[
![Build status](https://travis-ci.org/mathiasbynens/he.svg?branch=master)
](https://travis-ci.org/mathiasbynens/he)
[
![Code coverage status](https://codecov.io/github/mathiasbynens/he/coverage.svg?branch=master)
](https://codecov.io/github/mathiasbynens/he?branch=master)

*he*（「HTMLエンティティ」の略）は、JavaScriptで書かれた頑健なHTMLエンティティエンコーダ/デコーダです。HTML標準で定義されたすべての名前付き文字参照をサポートし、アンダースコアの曖昧さやその他のエッジケースをブラウザと同様に処理し、Unicodeを完全にサポートし、十分にテストされています。

このライブラリは積極的にメンテナンスされており、[jsdom](https://github.com/jsdom/jsdom) や [marked](https://github.com/markedjs/marked) などのプロジェクトで使用されています。

## インストール

npm を使用して:

```bash
npm install he
```

## 使い方

### ブラウザ上で

```html
<script src="he.js"></script>
```

または、ESモジュールを使用して:

```html
<script type="module">
  import he from './he.esm.js';
  console.log(he.decode('foo &copy; bar'));
</script>
```

### Node.js上で

```javascript
const he = require('he');

const html = 'foo © bar ≠ baz 𝌆 qux';
const encoded = he.encode(html);
console.log(encoded);
// → 'foo &#xA9; bar &#x2260; baz &#x1D306; qux'
```

### コマンドラインから

```bash
# 文字列をエンコード
$ he --encode 'foo © bar'
# → foo &#xA9; bar

# 文字列をデコード
$ he --decode 'foo &copy; bar'
# → foo © bar

# CLIにパイプで入力
$ echo 'foo &copy; bar' | he --decode
# → foo © bar
```

その他のCLIオプションについては `he --help` を参照してください。

```markdown
## API

### `he.encode(string, [options])`

この関数は、任意の文字列をエンコードします。

```javascript
const html = 'foo © bar ≠ baz 𝌆 qux';
const encoded = he.encode(html);
console.log(encoded);
// → 'foo &#xA9; bar &#x2260; baz &#x1D306; qux'
```

オプションの `options` オブジェクトは以下のプロパティをサポートします。

- **`useNamedReferences`**: ブール値。`true` の場合、可能な限り名前付き文字参照（例: `&copy;`）を使用します。デフォルトは `false` です。

  ```javascript
  he.encode('foo © bar', {
    'useNamedReferences': true
  });
  // → 'foo &copy; bar'
  ```

- **`decimal`**: ブール値。`true` の場合、10進数の数値文字参照（例: `&#169;`）を使用します。デフォルトは `false` で、16進数の文字参照（例: `&#xA9;`）が使用されます。

  ```javascript
  he.encode('foo © bar', {
    'decimal': true
  });
  // → 'foo &#169; bar'
  ```

- **`encodeEverything`**: ブール値。`true` の場合、すべての文字がエンコードされます（ASCII文字も含む）。デフォルトは `false` です。

- **`strict`**: ブール値。`true` の場合、無効なUnicodeコードポイントが見つかるとエラーがスローされます。デフォルトは `false` で、無効なコードポイントは `\uFFFD` に置き換えられます。

- **`allowUnsafeSymbols`**: ブール値。`true` の場合、`&`, `<`, `>`, `"`, `'`, `` ` `` の記号はエンコードされません。このオプションは `encodeEverything` が `false` の場合にのみ適用されます。デフォルトは `false` です。

### `he.decode(string, [options])`

この関数は、任意のHTMLテキストをデコードします。

```javascript
const html = 'foo &copy; bar &ne; baz &#x1D306; qux';
const decoded = he.decode(html);
console.log(decoded);
// → 'foo © bar ≠ baz 𝌆 qux'
```

オプションの `options` オブジェクトは以下のプロパティをサポートします。

- **`isAttributeValue`**: ブール値。`true` の場合、曖昧なアンパサンドがより柔軟に処理されます。HTML属性値をデコードする際にはこのオプションを有効にすべきです。デフォルトは `false` です。

- **`strict`**: ブール値。`true` の場合、数値文字参照が無効なコードポイントを指している場合（例: サロゲートコードポイント）にエラーがスローされます。デフォルトは `false` で、そのようなエンティティはそのままになります。

### `he.escape(string)`

この関数は、文字列内の `&`, `<`, `>`, `"`, `'`, `` ` `` の記号をエスケープします。これは `he.encode(string, { 'allowUnsafeSymbols': true, 'encodeEverything': false })` のショートカットです。

```javascript
const html = '<img src="x" onerror="alert(1)">';
const escaped = he.escape(html);
console.log(escaped);
// → '&lt;img src=&quot;x&quot; onerror=&quot;alert(1)&quot;&gt;'
```

### `he.unescape(string, [options])`

これは `he.decode(string, [options])` のエイリアスです。

### `he.version`

セマンティックバージョン番号を表す文字列です。

### デフォルトオプションの変更

グローバルなデフォルト設定は、`he.encode.options` および `he.decode.options` オブジェクトを変更することで変更できます。

```javascript
// デフォルトで名前付き参照を使用する
he.encode.options.useNamedReferences = true;

he.encode('foo © bar');
// → 'foo &copy; bar'
```

## サポート

`*he*` は Node.js およびすべての最新ブラウザでテストされ、動作します。また、以下の古い JavaScript 環境でもテストされています:
- Narwhal
- PhantomJS
- Rhino
- RingoJS

## 著者

| [
![twitter/mathias](https://gravatar.com/avatar/24e08a9ea84deb17ae121074d0f17125?s=70)
](https://twitter.com/mathias "Twitterで@mathiasをフォロー") |
|---|
| [Mathias Bynens](https://mathiasbynens.be/) |

## ライセンス

*he* は [MIT](LICENSE-MIT.txt) ライセンスのもとで利用できます。
