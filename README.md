# React JSX

## Overview

In this lesson, we'll discuss using JSX in our React code.

## Objectives

1. Learn JSX
2. Compiling with Babel and Webpack
3. Render a React component using JSX

## What's JSX?
Simply put, JSX allows us to write XML in our JavaScript files. Wait, you say, we can already do this! We just save our XML in strings! Yep, but this is different. JSX is where JS and XML/HTML _meet_, creating a very special and extremely productive marriage.

Up until now, we've been writing our components and elements using `React.createElement()`. JSX allows us to write these components in a way that is very similar to the end result we see in the browser (i.e. HTML), and is _much_ faster to write. It'll blow your mind — I promise.

![Using JSX](https://media.giphy.com/media/l4HnT4tZzUozNFx4s/giphy.gif)

## What does it look like?

A React component written in JSX looks something like this:

```jsx
class Tweet extends React.Component {
  render() {
    return (
      <div className="tweet">
        <img src="http://twitter.com/some-avatar.png" className="tweet__avatar" />
        <div className="tweet__body">
            <p>We are writing this tweet in JSX. Holy moly!</p>  
        </div>
      </div>
    );
  }
}
```

Isn't this great? Look how readable it is! It's _literally_ HTML, but in our JS! Looking at this code, there are some important things to note. First of all, JSX is _not_ a string — it's not in between quotes. Think of it as another type in JavaScript. Secondly, the parentheses containing the JSX are entirely optional, but recommended by convention.

And last...

...Wait, do you hear that? What's that outside the window?

![Angry mob](https://media.giphy.com/media/26uf4r3EldfX5Ykqk/giphy.gif)

It's an angry mob! We're mixing HTML and JS! We've created an abomination! _Blasphemy!_

Believe it or not (especially after seeing how cool JSX is), some developers _despise_ using JSX. They think mixing languages like this is something that should never be done. However, if you give it a little bit of time, you'll soon notice that the benefits far outweigh the (imaginary) cost of writing HTML in your JS. We've been doing it all along in the front-end using strings, this is just easier! Our component code is usually tightly intertwined with the representation of said component, so why not house them together in one file? It's convenient!

It's important to note that using JSX is _entirely_ optional. If writing HTML in your JavaScript feels like committing a mortal sin, it's okay to use `React.createElement()`. You'll be less productive, but at least you'll feel good about it!

Once we have our JSX, we'll need to perform an additional step to get this to work in our code. While this is valid JSX code, it's not something that browsers understand. To compile our code into something that the browsers understand, we need to transpile our code down into working JS (ES5 code) that all browsers can handle. More on that later!

## Gotchas
In the above code, you'll see that we're returning _one_ XML element (the `.tweet` div). JSX always has one, and _only_ one element (that optionally has children, grandchildren, and so on). You'd think we could do something like this:

```jsx
return (
  <p>I am the first paragraph</p>
  <p>I am the second paragraph</p>
);
```

But we can't! We'd have to wrap these two paragraphs in a `div` element:

```jsx
return (
  <div>
    <p>I am the first paragraph</p>
    <p>I am the second paragraph</p>
  </div>
);
```

The reason for this becomes abundantly clear when we take a look at the compiled output for the above code sample. This is the same code, but compiled down to regular JS:

```jsx
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

## Babel and Webpack
![Tower of Babel](http://www.ancient-origins.net/sites/default/files/field/image/tower-of-babel-2.jpg)

Earlier on in the lesson, we talked about how we'd use something called [Babel][Babel] to compile our JSX down to browser-readable JavaScript. Babel also allows us to use new JS features before they are standardized and implemented in the browser, allowing us to write the most modern code we can, without worrying about browser support. Babel transpiles everything back down to JavaScript that _all_ browsers can understand. Neat!

In the last couple of labs we have been using `npm start` to run our code in the browser and `npm test` to run our tests. The commands have been running Webpack and Babel to transpile our code into readable JS for all browsers. If you take a look in the root directory you will see a `.babelrc` file. This contains the Babel plugins that we use to transpile our code. 

[Webpack][Webpack] lets us require modules using Node's version of the CommonJS module system. This means that we can include modules in our file (both local files as well as `node_modules` installed with `npm`). When compiling a file with Webpack, it'll check every file for stuff that it needs to import, and also include that code. In more technical terms, it's traversing the dependency tree and inlining those dependencies in our script. What we'll end up with is one big JS file that includes _all_ of our code, including any dependencies (like `React`, or our own components) in that file too. That way, we also only need to have one script reference in our HTML: the bundled version!

One of the benefits of Webpack is that it wraps every module in an [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE), ensuring that no variable is global (unless you force it by setting something on `window`). This allows for pretty powerful modularization — we only export what we want other modules to use, and the rest is 'private' by default.

Webpack also lets us _transform_ the code we're bundling. That's where Babel comes in again: we'll use a Babel transform to compile our code down to readable JS that browsers understand.

## Writing Modular Code

Let's practice writing some modular code in our application. 

Our `index.js` file is still empty at this point. Let's practice writing modular code by creating a new file in `/src/components/foo.js` (you'll also need to create the `/src/components/` directory). In that file, we'll add this content:

```js
export const message = "I am a component!";
```

We can import this component in our `index.js` by using `import` and referencing the origin file:

```js
import { message } from './components/foo';
```

Note that files are always referred to using a relative path (even if they are in the same directory). This way Node knows whether to look for a local module or one found in `node_modules`, or in the global modules. Adding the `.js`  extension is not required.

Back to the exporting stuff! Using CommonJS, we have two options of exporting things out of our files: either through named exports (exporting multiple things) or a default export (exporting one thing).


### Named exports
Named exports allow us to export several things at once. This is useful for utility modules or libraries. Exporting several things at once is done by exporting an object. Because we are exporting this object as default without a name, we can assign it any name when we import it (in this case "fruit").

```js
// In a file called `fruits.js`
export default {
  apple: 'red',
  banana: 'yellow',
};

// In a file in the same directory
import fruit from './fruits';
console.log(fruit.apple); // prints 'red'

// In another file, also in the same directory
import { apple } from './fruits';
console.log(apple); // prints 'red'
```

When using named exports, we can choose to either import the entire thing and then reference the keys on the exported object, or we can import one specific key.

### Default export
A default export means we're exporting just one thing. This is useful for exporting components in their own file, since there's only one thing there: the component itself. Exporting one thing only is done by exporting a reference to what we want to export. You can also inline the value of what you want to export.

```jsx
// In a file called `Tweet.js`
import React from 'react';

class Tweet extends React.Component {
  render() {
    return (
      <div className="tweet">
        <img src="http://twitter.com/some-avatar.png" className="tweet__avatar" />
        <div className="tweet__body">
          <p>We are writing this tweet in JSX. Holy moly!</p>  
        </div>
      </div>
    );
  }
}

export default Tweet;

// In a file in the same directory
import Tweet from './Tweet';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <Tweet />,
  document.getElementById('root')
);
```

You'll mostly be using this method. It's important to correctly export your components, otherwise the tests can't access the code you've written, causing them to fail!

## Future labs
It's very important to know how this stuff works on a high level, because most of the React code nowadays is being compiled in one way or another — be it using Webpack, Browserify or something else. However, we don't want to create unnecessary busywork for you. Every lab from now on already has the bundling stuff set up for you. You just need to run `npm start` to start the compiling process. This will watch your code anytime you save your code and reload your browser. That's it!

## Resources
- [Webpack]: http://webpack.github.io
- [Babel]: http://babeljs.io/
- [Babelify]: https://github.com/babel/babelify
- [JSX]: https://facebook.github.io/react/docs/jsx-in-depth.html

<p class='util--hide'>View <a href='https://learn.co/lessons/react-jsx'>JSX</a> on Learn.co and start learning to code for free.</p>
