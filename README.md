# Incremental DOM

## Overview

Incremental DOM is a library for building up DOM trees and updating them in-place when data changes. It differs from the established virtual DOM approach in that no intermediate tree is created (the existing tree is mutated in-place). This approach significantly reduces memory allocation and GC thrashing for incremental updates to the DOM tree therefore increasing performance significantly in some cases.

Incremental DOM is primarily intended as a compilation target for templating languages. It could be used to implement a higher level API for human consumption. The API was carefully designed to minimize heap allocations and where unavoidable ensure that as many objects as possible can be de-allocated by incremental GC. One unique feature of its API is that it separates opening and closing of tags so that it is suitable as a compilation target for templating languages that allow (temporarily) unbalanced HTML in templates (e.g. tags that are opened and closed in separate templates) and arbitrary logic for creating HTML attributes.
*Think of it as ASM.dom.*

## Supported Browsers

Incremental DOM supports IE9 and above.

## Usage

HTML is expressed in Incremental DOM using the `elementOpen`, `elementClose`, `elementVoid` and `text` methods. Consider the following example:

```javascript
import {
  elementVoid,
  elementOpen,
  elementClose,
  text
} from "https://deno.land/x/idom/mod.ts";

function render (data) {
  elementVoid('input', '', [ 'type', 'text' ]);
  elementOpen('div', '', null);
    if (data.someCondition) {
      text(data.text);
    }
  elementClose('div');
}
```

To render or update an existing DOM node, the patch function is used:

```javascript
import {
  patch
} from "https://deno.land/x/idom/mod.ts";

const data = {
  text: 'Hello deno!',
  someCondition: true
};

patch(document.body, function() {
  render(data);
});

data.text = 'Hello World!';

patch(document.body, function() {
  render(data);
});
```

## Templating Languages and Libraries

[Check out](ECOSYSTEM.md)  what others having been doing with Incremental DOM.

## Docs

- [Introducing Incremental Dom](https://medium.com/google-developers/introducing-incremental-dom-e98f79ce2c5f)
- [Docs and demos](http://google.github.io/incremental-dom/)

### Building

Use deno bundle to build for the browser.

E.g.

`deno bundle -c tsconfig.json src/app.ts dist/app.bundle.js`

In your `tsconfig.json`, make sure you include:

```json
{
  "compilerOptions": {
    "lib": ["es2015", "dom"]
  }
}
```

Alternatively, use `Deno.emit()` to bundle.

```javascript
const { diagnostics, files, stats } = await Deno.emit("app.ts", {
  bundle: "esm",
  compilerOptions: {
    lib: ["es2015", "dom"],
  }
});

console.log(diagnostics, files, stats);
```

Once you have a bundle created, just import it in your html:

```html
<script type="module" src="dist/app.bundle.js">
</script>
```

<!---
## Getting Incremental DOM

### Via CDN

https://ajax.googleapis.com/ajax/libs/incrementaldom/0.5.1/incremental-dom.js
https://ajax.googleapis.com/ajax/libs/incrementaldom/0.5.1/incremental-dom-min.js

### Using npm

```sh
npm install incremental-dom
```

## Development

To install the required development packages, run the following command:

```sh
npm i
```

### Running tests

To run once:

```sh
./node_modules/.bin/bazel test ...
```

To run on change:

```sh
./node_modules/.bin/ibazel run ...
```

### Building

To build once:

```sh
./node_modules/.bin/bazel build ...
```

To build on change:

```sh
./node_modules/.bin/ibazel ...
```
-->