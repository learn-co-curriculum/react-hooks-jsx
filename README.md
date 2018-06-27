# React JSX

## Overview

In this lesson, we'll discuss using JSX in our React code.

## Objectives

1. Take a deeper look at JSX
2. Explore some of the unique syntax for JSX
3. Understand what is required to write valid JSX code

## What's JSX?

Simply put, JSX allows us to write HTML-like code in our JavaScript files. JSX
is a syntax extension of JavaScript that creates a very special and extremely
productive marriage between HTML and JS.

JSX allows us to write React components in JavaScript files in an efficient and
expressive manner. Ultimately, JSX looks a lot like the end result we see in the
browser (i.e. HTML), and is _much_ faster to write, especially when
incorporating a lot of JavaScript and dynamic content.

![Using JSX](https://media.giphy.com/media/l4HnT4tZzUozNFx4s/giphy.gif)

## What does it look like?

A React component written in JSX looks something like this:

```js
class Tweet extends React.Component {

  currentTime = () => new Date().toString()

  render() {
    return (
      <div className="tweet">
        <img src="http://twitter.com/some-avatar.png" className="tweet__avatar" />
        <div className="tweet__body">
            <p>We are writing this tweet in JSX. Holy moly!</p>
            <p>{ Math.floor(Math.random()*100) retweets }</p>
            <p>{ this.currentTime() }</p>
        </div>
      </div>
    );
  }
}
```

Isn't this great? Look how readable it is! It's HTML, but in our JS. Looking at
this code, there are some important things to note:

###### JSX is _not_ a String

The JSX in the example about is not wrapped in quotes. Think of it as another
type in JavaScript.

###### JSX is the return value of the `render()` method

Every component you use needs a `render()` method that returns some valid JSX.
Although our example displays six lines of JSX, this is done for readability
only. The entire return statement is wrapped in parentheses so it is considered
one 'chunk' of JSX code.

###### JSX can include JavaScript

While writing our pseudo-HTML, JSX, we can also write vanilla JavaScript
_in-line_. We do this by wrapping the JavaScript code in curly braces. In the
example, we call the `Math.floow()`  and `Math.random()` methods directly,
which will generate a random number of retweets.

We _also_ called a custom function, `currentTime()`, which returns the String
value of the current date and time. In this case, since `currentTime()` is
another method within the Tweet class, we must prepend `currentTime()` with
`this`:

```js
<p>{ this.currentTime() }</p>
```

**Note:** Syntax is important here!Since we're using an arrow function for the
`currentTime()`, we are _implicitly binding_ the method to this class.  

We could us the older function format to define `currentTime()`:

```js
currentTime() {
  return new Date().toString()
}
```

... this will still work currently, but getting acquainted with using arrow
functions now will save headaches later and looks cleaner. As we get into
_props_ in React, we sometimes need to call functions like `currentTime()` in a
class _different from its origin_. When this happens, without using the arrow
function, we end up needing to explicitly bind methods to the class their
_originally from_, and causing to write code like this sometimes:

```js
this.currentTime().bind(this)
```

###### A component must return only one JSX element

In all the lesson examples we've seen so far, each component is returning a
`div` that contains child elements.  In our first example, the `Tweet` class
returns a `div` with the class, "tweet". This `div` contains two children, an
`img` and an additional `div` element. We can actually use any HTML element we
would normally use to contain content.  The following are all valid components:

```JavaScript
class PlainDiv extends Component {
  render() {
    return <div>I am one line, so I do not need the parentheses</div>
  }
}

class Photo extends Component {
  render() {
    return (
        <figure>
          <img className="image" src="https://s3.amazonaws.com/ironboard-learn/sunglasses.gif" />
          <figcaption>Whoa</figcaption>
        </figure>
    )
  }
}

class Table extends Component {
  render() {
    return (
      <table>
        <tr>
          <th>ID</th>
          <th>Name</th>
        </tr>
        <tr>
          <th>312213</th>
          <th>Tim Berners-Lee</th>  
        </tr>
      </table>
    )
  }
}

class ParentComponent extends Component {
  render() {
    return (
      <main>
        <PlainDiv />
        <Photo />
        <Table />
      </main>
    )
  }
}
```

Each of these is valid, but _all_ of these components have _one_ returned
JSX element that contains everything else. There is one exception to this.

## Fragments

It is true that every React component must return a single JSX element, and we often use the HTML-like elements such as `div`. When rendered, this creates a DOM element for that outer `div`, which is sometimes unnecessary.

```js
class ChildComponent extends Component {
  render() {
    return (
      <div>
        <p>Hey, I am a child</p>
      </div>
    )
  }
}

class ParentComponent extends Component {
  render() {
    return (
      <div>
        <ChildComponent />
        <ChildComponent />
      </div>
    )
  }
}

//this set up creates a DOM structure that looks like this:
<div>
  <div>
    <p>Hey, I am a child</p>
    <p>My name is child component</p>
  </div>
  <div>
    <p>Hey, I am a child</p>
    <p>My name is child component</p>
  </div>
</div>
```

Those nested `div`s don't have any purpose here and don't have any styling.
Without them though, we would have an error as there are _two_ `p` tags being
returned in the ChildComponent. Instead, we could use JSX Fragments,
preventing the extra `div`s from being added to the DOM:

```js
class ChildComponent extends Component {
  render() {
    //The wrapping 'div' here has been replaced with a fragment
    return (
      <>
        <p>Hey, I am a child</p>
        <p>My name is child component</p>
      </>
    )
  }
}

class ParentComponent extends Component {
  render() {
    return (
      <div>
        <ChildComponent />
        <ChildComponent />
      </div>
    )
  }
}

// with the fragment in place, the DOM will now look like this:
<div>
    <p>Hey, I am a child</p>
    <p>My name is child component</p>
    <p>Hey, I am a child</p>
    <p>My name is child component</p>
</div>
```

The `<>` and `</>` are shorthand for `<React.Fragment>` and `</React.Fragment>`
and can be used interchangeably. They allow a component to return multiple
elements _without_ adding a wrapper element that adds to the DOM.

## Avoiding Keywords

One thing to note about JSX is that, since we're still writing JS code, we need to
avoid using keywords in our code. You might have noticed it already: we're
setting HTML classes using the `className` attribute (or prop, in React terms),
instead of `class`. This is because `class` is a reserved keyword in JavaScript!
The same thing is true for the `for` label, which is another keyword in JS. If
you want to use the HTML `for` attribute, you'd use `htmlFor` instead.

## When to Split Content Into Components

When to split content out into individual components is really up to the
designer, but it is often done

* at logical breaks within the code. A table, for instance, would make sense as a
self-contained component
* whenever there is some repeating, dynamic content. A list of multiple comments
may have different text content, but their structure is consistent, so it can be
abstracted away into _one_ component that takes in dynamic content for each
comment
* whenever the same JSX is needed in multiple places. Maybe an avatar image is
used in a website's navigation bar _as well as_ in comments. This could be
_one_ component used in two different parts of the page
* for readability! You _could_ write an entire React app in _one_ component, but by
separating chunks of JSX out, it becomes easier to understand and navigate a
complex web app. In the `ParentComponent` class above, we can see how the other
components will be ordered on the page _without_ needing to see what is actually
inside `<PlainDiv />`, `<Photo />` or `<Table />`
* for long term reusability. It is possible to write components that are
abstracted enough, they can be used in multiple projects. Maybe you write a
sweet navigation bar once and use it for four or five different projects!

## A Quick Note on Exporting

As with good programming practices, we want
to write modular code. That is, we want to split our code up into logical
sections/separate files. Let's practice how we do just that in React:

Go ahead and create a `/src` directory in this repository and add `index.js` to it.

Let's practice writing modular code by creating a new file in
`/src/components/foo.js` (you'll also need to create the `/src/components/`
directory). In that file, we'll add this content:

```js
export const message = "I am a component!";
```

We can import this component in our `index.js` by using `import` and referencing the origin file:

```js
import { message } from './components/foo';
```

Note that files are always referred to using a relative path (even if they are
in the same directory). This way Node knows whether to look for a local module,
one found in `node_modules`, or in the global modules.

Back to the exporting stuff! Using CommonJS, we have two options of exporting
things out of our files: either through named exports (exporting multiple
things) or a default export (exporting one thing).

### Named exports

Named exports allow us to export several things at once. This is useful for
utility modules or libraries. Exporting several things at once is done by
exporting an object. Because we are exporting this object as default without a
name, we can assign it any name when we import it (in this case "fruit").

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

When using named exports, we can choose to either import the entire thing and
then reference the keys on the exported object, or we can import one specific
key.

### Default export

A default export means we're exporting just one thing. This is useful for
exporting components in their own file, since there's only one thing there: the
component itself. Exporting one thing only is done by exporting a reference to
what we want to export. You can also inline the value of what you want to
export.

```js
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

You'll mostly be using this method. It's important to correctly export your
components, otherwise the tests can't access the code you've written, causing
them to fail!

## Future labs

It's very important to know how this stuff works on a high level, because most
of the React code nowadays is being compiled in one way or another. However, we
don't want to create unnecessary busywork for you. Every lab from now on already
has the JSX transforming stuff set up for you. You just need to run `npm
install` and `npm start` to execute the JSX --> React run-able JavaScript
process.

## Resources
- Webpack: http://webpack.github.io
- Babel: http://babeljs.io/
- Babelify: https://github.com/babel/babelify
- JSX: https://facebook.github.io/react/docs/jsx-in-depth.html


Our component code is usually tightly intertwined with the representation of
said component, so why not house them together in one file? It's convenient!


<p class='util--hide'>View <a href='https://learn.co/lessons/react-jsx'>JSX</a> on Learn.co and start learning to code for free.</p>


## Gotchas

In the above code, you'll see that we're returning _one_ XML element (the
`.tweet` div). JSX always has one, and _only_ one element (that optionally has
children, grandchildren, and so on). You'd think we could do something like
this:

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

The reason for this becomes abundantly clear when we take a look at the compiled
output for the above code sample. This is the same code, but compiled down to
regular JS:

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
``
