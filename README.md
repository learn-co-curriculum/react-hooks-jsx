# React JSX

## Objectives

1. Learn how to compile JSX to plain JavaScript
2. Set up [browserify][browserify] and
   [babelify][babelify] for compiling your application.
3. Render a React component using JSX

## Overview

This lesson needs to cover *a lot*. Rather than dragging students through the
mud of inlining their dependencies for the foreseeable future (and using global
variables, etc.), it seems best to just set them up with a decent compilation
step here.

Just spitballing, but I think it might be nice to have them write a `bin/build`
script (in JavaScript) that makes use of browserify's Node.js API. They can then
add `"build": "node bin/build"` to the package.json's `"scripts"` field to get a
sense of how that all works. (One aim here is to demystify some of the command
line magic — it's just scripts!)

Make sure they understand the presets that they'll need (`react` and `es2015`
should do it for now).

Finally, have students write a simple component using JSX (emphasizing that it's
really _just JavaScript_) and render it in the DOM (with the build step). So in
other words, it would be awesome to have students set up some kind of basic
directory structure:

```
index.js
components/
|
|_ HelloWorld.js
```

Where in their `index.html` they're including

```html
<script src="build/index.js"></script>
```

(Or wherever you'd prefer to teach them to put the compiled JavaScript — be sure
to add this file to the `.gitignore`.)

This way, what they're doing in `index.js` might look like

```javascript
const React = require('react')
const ReactDOM = require('react-dom')
const HelloWorld = require('./components/HelloWorld')

ReactDOM.render(<HelloWorld />, document.getElementById('main'))
```

as long as `components/HelloWorld.js` looks like

```javascript
const React = require('react')

class HelloWorld extends React.Component {
  render() {
    return <h1>Hello, world!</h1>
  }
}

module.exports = HelloWorld
```

I realize `module.exports` and `require` can get a little hairy. Feel free to
simplify as much as possible.

## Resources

- [browserify]: https://github.com/substack/node-browserify
- [babelify]: https://github.com/babel/babelify
