# React JSX

## Overview 

In this lesson, we'll discuss using JSX in our React code and how to transpile this code from JSX to JS that browsers understand. We'll do this by bundling our scripts using Browserify, and transforming the JSX using Babel.

## Objectives

1. Compile JSX to plain JavaScript
2. Set up [browserify](https://github.com/substack/node-browserify) and [babelify](https://github.com/babel/babelify)for compiling an application
3. Render a React component using JSX

## What's JSX?
Simply put, JSX allows us to write XML in our JavaScript files. Wait, you say, we can already do this! We just save our XML in strings! Yep, but this is different. JSX is where JS and XML/HTML _meet_, creating a very special and extremely productive marriage.

Up until now, we've been writing our components and elements using `React.createElement()`. JSX allows us to write these components in a way that is very similar to the end result we see in the browser (i.e. HTML), and is _much_ faster to write. It'll blow your mind — I promise.

![Using JSX](https://media.giphy.com/media/l4HnT4tZzUozNFx4s/giphy.gif)

## What does it look like?

A React component written in JSX looks something like this:

```js
class Tweet extends React.Component {
  render() {
    return (
      <div className="tweet">
        <img src="http://twitter.com/some-avatar.png" className="tweet__avatar" />
        <div className="tweet__body">
            <p>We're writing this tweet in JSX. Holy moly!</p>  
        </div>
      </div>
    );
  }
}
```

Isn't this great? Look how readable it is! It's _literally_ HTML, but in our JS! Looking at this code, there are some important things to note. First of all, JSX is _not_ a string — it's not in between quotes. Think of it as another type in JavaScript. Secondly, the parentheses containing the JSX are entirely optional, but recommended by convention.

And las...

...Wait, do you hear that? What's that outside the window?

![Angry mob](https://media.giphy.com/media/26uf4r3EldfX5Ykqk/giphy.gif)

It's an angry mob! We're mixing HTML and JS! We've created an abomination! _Blasphemy!_

Believe it or not (especially after seeing how cool JSX is), some developers _despise_ using JSX. They think mixing languages like this is something that should never be done. However, if you give it a little bit of time, you'll soon notice that the benefits far outweigh the (imaginary) cost of writing HTML in your JS. We've been doing it all along in the front-end using strings, this is just easier! Our component code is usually tightly intertwined with the representation of said component, so why not house them together in one file? It's convenient!

It's important to note that using JSX is _entirely_ optional. If writing HTML in your JavaScript feels like committing a mortal sin, it's okay to use `React.createElement()`. You'll be less productive, but at least you'll feel good about it!

Once we have our JSX, we'll need to perform an additional step to get this to work in our code. While this is valid JSX code, it's not something that browsers understand. To compile our code into something that the browsers understand, we use Babel that transpiles our code down to working JS that all browsers can handle. More on that later!

## Gotchas
In the above code, you'll see that we're returning _one_ XML element (the `.tweet` div). JSX always has one, and _only_ one element (that optionally has children, grandchildren, and so on). You'd think we could do something like this:

```js
return (
  <p>I am the first paragraph</p>
  <p>I am the second paragraph</p>
);
```

But we can't! We'd have to wrap these two paragraphs in a `div` element:

```js
return (
  <div>
    <p>I am the first paragraph</p>
    <p>I am the second paragraph</p>
  </div>
);
```

The reason for this becomes abundantly clear when we take a look at the compiled output for the above code sample. This is the same code, but compiled down to regular JS:

```js
return React.createElement(
  "div",
  null,
  React.createElement(
    "p",
    null,
    "I am the first paragraph"
  ),
  React.createElement(
    "p",
    null,
    "I am the second paragraph"
  )
);
```

As you can see, it's returning _one_ element with its children. Not being able to just write two `<p>` elements in our earlier code sample now makes sense — returning two values at once in JavaScript is conceptually impossible.

Another thing to note is that since we're still writing JS code, we need to avoid using keywords in our code. You might have noticed it already: we're setting HTML classes using the `className` attribute (or prop, in React terms), instead of `class`. This is because `class` is a reserved keyword in JavaScript! The same thing is true for the `for` label, which is another keyword in JS. If you want to use the HTML `for` attribute, you'd use `htmlFor` instead.

## Babel and Browserify
![Tower of Babel](http://www.ancient-origins.net/sites/default/files/field/image/tower-of-babel-2.jpg)

Earlier on in the lesson, we talked about how we'd use something called [Babel](babeljs.io) to compile our JSX down to browser-readable JavaScript. Babel also allows us to use new JS features before they are standardised and implemented in the browser, allowing us to write the most modern code we can, without worrying about browser support. Babel transpiles everything back down to JavaScript that _all_ browsers can understand. Neat!

We won't use Babel as-is, though. Starting now, we're going to write our code in a more modular fashion, splitting things up in separate files when it makes sense. _Sigh_. Does that mean we're going to need to add a script reference in our HTML for _every_ little file we're going to make? Nope! That's where our second new technology comes in: Browserify!

[Browserify](http://browserify.org/) lets us require modules using [Node's version of the CommonJS module system](https://github.com/substack/browserify-handbook#node-packaged-modules). This means that we can include modules in our file (both local files as well as `node_modules` installed with `npm`). When compiling a file with Browserify, it'll check every file for stuff that it needs to import, and also include that code. In more technical terms, it's traversing the dependency tree and inlining those dependencies in our script. What we'll end up with is one big JS file that includes _all_ of our code, including any dependencies (like `React`, or our own components) in that file too. That way, we also only need to have one script reference in our HTML: the bundled version!

One of the benefits of Browserify is that it wraps every module in an [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE), ensuring that no variable is global (unless you force it by setting something on `window`). This allows for pretty powerful modularization — we only export what we want other modules to use, and the rest is 'private' by default.

Browserify also lets us _transform_ the code we're bundling. That's where Babel comes in again: we'll use a Babel transform to compile our code down to readable JS that browsers understand.

## Nuts and bolts
Let's see if we can get this to work! First of all, we'll need to install the `browserify` module:

```
$ npm install --save-dev browserify
```

Once that's done, we'll compile our code using the `browserify` command. We can do so by running the following:

```
$ browserify index.js -o bundle.js
```

The first argument is the path to our main JS file (that is responsible for importing all other files). Next, we tell it where we want the output bundle to go. In this case, that's the same directory, in a file called `bundle.js`. When you run the command above, chances are you'll run into an error that says `browserify` is not found. That's totally fine! It's not globally installed, but we don't need it to be, because we'll build our JS using an `npm` script. Taking a look at the `package.json` file, we'll find an object called `scripts` that contains various scripts we can run in our project. We'll add another script now that builds our JS:

```json
"scripts": {
  ... (other scripts)
  "bundle": "browserify index.js -o bundle.js",
}
```

Now, we'll run the script using `npm run bundle`. Wait, now it does work? Why's that? Well, `npm` knows `browserify` is a module that has an executable (or a binary), and links it in `node_modules/.bin/`. When `npm run` runs a script, it first checks if any command there can be found in that `.bin` folder, before using the system-wide commands. Handy!

Our `index.js` file is still empty at this point though. Let's practice writing modular code by creating a new file in `components/foo.js` (you'll also need to create the `components/` directory). In that file, we'll add this content:

```js
module.exports = 'I am a component!';
```

We'll get to what the `module.exports` stuff is in just a second. We can import this component in our `index.js` by using `require()` and assigning the result to a variable:

```js
const component = require('./components/foo');
```

Note that files are always referred to using a relative path (even if they are in the same directory). This way Node knows whether to look for a local module or one found in `node_modules`, or in the global modules. Adding the `.js`  extension is not required.

Back to the exporting stuff! Using CommonJS, we have two options of exporting things out of our files: either through named exports (exporting multiple things) or a default export (exporting one thing).


### Named exports
Named exports allow us to export several things at once. This is useful for utility modules or libraries. Exporting several things at once is done by exporting an object when setting the value of `module.exports`.

```js
// In a file called `fruits.js`
module.exports = {
  apple: 'red',
  banana: 'yellow',
};

// In a file in the same directory
const fruits = require('./fruits');
console.log(fruits.apple); // prints 'red'

// In another file, also in the same directory
const apple = require('./fruits').apple;
console.log(apple); // prints 'red'
```

When using named exports, we can choose to either import the entire thing and then reference the keys on the exported object, or we can import one specific key. The last example can also be rewritten using ES2015 destructuring syntax:

```js
const { apple } = require('./fruits');
console.log(apple); // prints 'red'
```

### Default export
A default export means we're exporting just one thing. This is useful for exporting components in their own file, since there's only one thing there: the component itself. Exporting one thing only is done by exporting a reference to what we want to export when setting the value of `module.exports`. You can also inline the value of what you want to export.

```js
// In a file called `Tweet.js`
const React = require('react');

class Tweet extends React.Component {
  render() {
    return (
      <div className="tweet">
        <img src="http://twitter.com/some-avatar.png" className="tweet__avatar" />
        <div className="tweet__body">
            <p>We're writing this tweet in JSX. Holy moly!</p>  
        </div>
      </div>
    );
  }
}

module.exports = Tweet;

// In a file in the same directory
const Tweet = require('./Tweet');

ReactDOM.render(
  <Tweet />,
  document.getElementById('main')
);
```

You'll mostly be using this method. It's important to correctly export your components, otherwise the tests can't access the code you've written, causing them to fail!

## Transforming the JSX
![Our code being transformed](https://media.giphy.com/media/FmW24votl9LdS/giphy.gif)

Now that we know how we can compile our bundle using Browserify, it's time to transform our JSX using Babel. Without the right transformer, Browserify won't know how to handle the JSX in our code and will fail, thinking the code we wrote has syntax errors in it.

To use a transformer, we pass the transformer name to the `browserify` command. First, we'll have to install some extra stuff:

```
$ npm install --save-dev babel-core babel-preset-es2015 babel-preset-react babelify
```

Let's quickly go through the modules we just installed. `babel-core` is Babel itself. Just by itself, Babel does absolutely _nothing_ to your files. You need to explicitly tell it what plugins to use. Luckily, there are transform _presets_ that form a collection of these separate plugins. That's what the `babel-preset-es2015` and `babel-preset-react` modules are for: they respectively transform our ES2015 and JSX code into JS that is understandable by browsers _today_. Lastly, we install `babelify` which is the transformer for Browserify.

We still need to let Babel know what presets we'd like to use. Babel reads its configuration from a `.babelrc` file in the project's root. This is a JSON file with Babel's options:

```json
{
  "presets": ["es2015", "react"]
}
```

Now all that's left to do is let Browserify know which transformer to use. In `package.json`, we'll update our `bundle` script to use the transformer:

```json
"scripts": {
  ... (other scripts)
  "bundle": "browserify index.js -t babelify -o bundle.js",
}
```

That should do the trick. Running `npm run bundle` should now correctly transpile our ES2015 and JSX code into one file called `bundle.js`. Success!

**Important: whenever writing JSX, _always_ import React (`const React = require('react');`). This is because Babel uses `React.createElement()` to compile our JSX down to JS. If `React` isn't found in that file, it'll throw an error!**

## Using the bundle
In the past, we've always loaded our source code in the HTML:

```html
<script src="index.js"></script>
```

Instead, now we'll reference our compiled (or bundled) code:

```html
<script src="bundle.js"></script>
```

That's the only script we need to add to our HTML file to make everything work. This bundle includes our source code as well as anything else we've imported in our code, like libraries (e.g. `React`, `ReactDOM` and others).

## Future labs
It's very important to know how this stuff works on a high level, because most of the React code nowadays is being compiled in one way or another — be it using Browserify, Webpack or something else. However, we don't want to create unnecessary busywork for you. Every lab from now on already has the bundling stuff set up for you. You just need to run `npm run bundle` any time you want to compile your changes and view them in the browser. That's it!

## Resources
- [Browserify](https://github.com/substack/node-browserify)
- [Babel](http://babeljs.io/)
- [Babelify](https://github.com/babel/babelify)
- [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html)
