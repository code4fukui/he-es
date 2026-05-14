# he

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

[
![Build status](https://travis-ci.org/mathiasbynens/he.svg?branch=master)
](https://travis-ci.org/mathiasbynens/he)
[
![Code coverage status](https://codecov.io/github/mathiasbynens/he/coverage.svg?branch=master)
](https://codecov.io/github/mathiasbynens/he?branch=master)

*he* (for “HTML entities”) is a robust HTML entity encoder/decoder written in JavaScript. It supports all named character references as defined by the HTML standard, handles ambiguous ampersands and other edge cases just like a browser would, has full Unicode support, and is well-tested.

This library is actively maintained and is used by projects like [jsdom](https://github.com/jsdom/jsdom) and [marked](https://github.com/markedjs/marked).

## Installation

Via [npm](https://www.npmjs.com/):

```bash
npm install he
```

## Usage

### In a browser

```html
<script src="he.js"></script>
```

Or, using ES modules:

```html
<script type="module">
  import he from './he.esm.js';
  console.log(he.decode('foo &copy; bar'));
</script>
```

### In Node.js

```javascript
const he = require('he');

const html = 'foo © bar ≠ baz 𝌆 qux';
const encoded = he.encode(html);
console.log(encoded);
// → 'foo &#xA9; bar &#x2260; baz &#x1D306; qux'
```

### Via the command line

```bash
# Encode a string
$ he --encode 'foo © bar'
# → foo &#xA9; bar

# Decode a string
$ he --decode 'foo &copy; bar'
# → foo © bar

# Pipe input to the CLI
$ echo 'foo &copy; bar' | he --decode
# → foo © bar
```

For more CLI options, see `he --help`.

## API

### `he.encode(string, [options])`

This function encodes any given string of text.

```javascript
const html = 'foo © bar ≠ baz 𝌆 qux';
const encoded = he.encode(html);
console.log(encoded);
// → 'foo &#xA9; bar &#x2260; baz &#x1D306; qux'
```

The optional `options` object supports the following properties:

- **`useNamedReferences`**: A boolean. If `true`, named character references (e.g. `&copy;`) are used where possible. The default value is `false`.

  ```javascript
  he.encode('foo © bar', {
    'useNamedReferences': true
  });
  // → 'foo &copy; bar'
  ```

- **`decimal`**: A boolean. If `true`, decimal numeric character references (e.g. `&#169;`) are used. The default is `false`, which means hexadecimal character references (e.g. `&#xA9;`) are used.

  ```javascript
  he.encode('foo © bar', {
    'decimal': true
  });
  // → 'foo &#169; bar'
  ```

- **`encodeEverything`**: A boolean. If `true`, all characters are encoded, including ASCII characters. The default is `false`.

- **`strict`**: A boolean. If `true`, an error is thrown when invalid Unicode code points are encountered. The default is `false`, which means invalid code points are replaced with `\uFFFD`.

- **`allowUnsafeSymbols`**: A boolean. If `true`, the symbols `&`, `<`, `>`, `"`, `'`, and `` ` `` are not encoded. This option is only applicable when `encodeEverything` is `false`. The default is `false`.

### `he.decode(string, [options])`

This function decodes any given string of HTML text.

```javascript
const html = 'foo &copy; bar &ne; baz &#x1D306; qux';
const decoded = he.decode(html);
console.log(decoded);
// → 'foo © bar ≠ baz 𝌆 qux'
```

The optional `options` object supports the following properties:

- **`isAttributeValue`**: A boolean. If `true`, ambiguous ampersands are handled more leniently. This option should be enabled when decoding HTML attribute values. The default is `false`.

- **`strict`**: A boolean. If `true`, an error is thrown when a numeric character reference points to an invalid code point (e.g. a surrogate code point). The default is `false`, which means such entities are left as-is.

### `he.escape(string)`

This function escapes the symbols `&`, `<`, `>`, `"`, `'`, and `` ` `` in a string. This is a shortcut for `he.encode(string, { 'allowUnsafeSymbols': true, 'encodeEverything': false })`.

```javascript
const html = '<img src="x" onerror="alert(1)">';
const escaped = he.escape(html);
console.log(escaped);
// → '&lt;img src=&quot;x&quot; onerror=&quot;alert(1)&quot;&gt;'
```

### `he.unescape(string, [options])`

This is an alias for `he.decode(string, [options])`.

### `he.version`

A string representing the semantic version number.

### Changing the default options

The global default settings can be modified by changing the `he.encode.options` and `he.decode.options` objects.

```javascript
// Use named references by default
he.encode.options.useNamedReferences = true;

he.encode('foo © bar');
// → 'foo &copy; bar'
```

## Support

*he* is tested and works in Node.js and all modern browsers. It is also tested in older JavaScript environments, including:
- Narwhal
- PhantomJS
- Rhino
- RingoJS

## Author

| [
![twitter/mathias](https://gravatar.com/avatar/24e08a9ea84deb17ae121074d0f17125?s=70)
](https://twitter.com/mathias "Follow @mathias on Twitter") |
|---|
| [Mathias Bynens](https://mathiasbynens.be/) |

## License

*he* is available under the [MIT](LICENSE-MIT.txt) license.
