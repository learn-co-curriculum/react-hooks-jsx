# JSX and Component Structure

## Overview

In this lesson, we'll discuss using JSX, its syntax and how it is works within
the context of React components.

## Objectives

1. Take a deeper look at JSX
2. Explore some of the unique syntax for JSX
3. Understand what is required to write valid JSX code

## What is JSX?

Simply put, JSX allows us to write HTML-like code in our JavaScript files. JSX
is a syntax extension of JavaScript that creates a very special and extremely
productive marriage between HTML and JS.

With JSX, we can write React components in an efficient and expressive manner.
Ultimately, JSX looks a lot like the end result we see in the browser (i.e.
HTML), and is _much_ faster to write, especially when incorporating a lot of
JavaScript and dynamic content.

![Using JSX](https://media.giphy.com/media/l4HnT4tZzUozNFx4s/giphy.gif)

## What does it look like?

React components return JSX within their `render()` methods:

```js
class Tweet extends Component {

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

Whoa, isn't this interesting? It's HTML, but in our JS... with JS _inside the
HTML!_ Looking at this code, there are some important things to point out:

###### JSX is _not_ a String

The JSX in the example about is not wrapped in quotes. Think of it as another
type in JavaScript. **We are not interpolating HTML Strings** like we do with
standard JavaScript DOM manipulation.

###### JSX is the return value of the `render()` method

Every component you use needs a `render()` method that returns some valid JSX.
Although our example displays six lines of JSX, this is done for readability
only. The entire return statement is wrapped in parentheses so it is considered
one 'chunk' of JSX code, with _one_ top level element (in this case, a `div`).

###### JSX can include JavaScript

While writing our pseudo-HTML, JSX, we can also write vanilla JavaScript
_in-line_. We do this by wrapping the JavaScript code in curly braces. In the
example, we call the `Math.floor()`  and `Math.random()` methods directly,
which will return a random number when the component is rendered.

We _also_ called a custom function, `currentTime()`, which returns the String
value of the current date and time. In our example, because `currentTime()` is
another method within the Tweet class, we must prepend `currentTime()` with
`this`:

```js
<p>{ this.currentTime() }</p>
```

**Note on Arrow Functions:** Syntax is important here! Since we're using an
arrow function for `currentTime()`,

```js
currentTime = () => new Date().toString()
```

...we are _implicitly binding_ the method to the Tweet class.

We _could_ use the class method format to define `currentTime()`:

```js
currentTime() {
  return new Date().toString()
}
```

...and this will still work in our current example, but getting acquainted with
using arrow functions now will save headaches later. As we get into _props_ in
React, we sometimes need to call functions like `currentTime()` in a class
_different from its origin_. When this happens, **without the arrow
function**, we often end up having to explicitly bind methods to the class their
_originally from_, causing us to write code like this:

```js
this.currentTime().bind(this)
```

Without the `.bind(this)`, the _first_ 'this' will refer to whatever object it
is in when called, which can be a _different_ component.

###### A component must return only one JSX element

In all the lesson examples we've seen so far, each component is returning a
`div` that contains content or child elements. However, we can actually use any
HTML element we would normally use to contain content.  The following are all
valid components:

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

Each of these is a valid component, but _all_ of these components have _one_
returned JSX element that contains everything else. Without an element that
wraps the returned JSX in a component, we will get an error. There are _some_
exceptions to this, such as [React fragments][frag], that we will look at, but most
often, we will be using the HTML-like JSX elements.

## Avoiding Keywords

One thing to note about JSX is that, since we're still writing JavaScript code,
we need to avoid using keywords in our code. You might have noticed it already:
we're setting HTML classes using the `className` attribute (or prop, in React
terms), instead of `class`. This is because `class` is a reserved keyword in
JavaScript! The same thing is true for the `for` label, which is another keyword
in JS. If you want to use the HTML `for` attribute, you'd use `htmlFor` instead.

## When to Split Content Into Components

_When_ to split JSX content out into individual components is really up to the
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
* for readability! You _could_ write an entire React app in _one_ component, but
by separating chunks of JSX out, it becomes easier to understand and navigate
the code of a complex web app. In the example `ParentComponent` class above, we
can see how the other components will be ordered on the page _without_ needing
to see what is actually inside `<PlainDiv />`, `<Photo />` or `<Table />`
* for long term reusability. It is possible to write components that are
abstracted enough, they can be used in multiple projects. Maybe you write a
sweet navigation bar once and use it for four or five different projects!

## Conclusion

In the early forms of React, instead of JSX, the code returned in components was
much less reader friendly. To create a React element, we would write things like this:

```js
React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

While JSX introduces some new rules we must follow, the benefit is that we can
write code that is semantic and _declarative_. Writing this:

```js
<h1 className='greeting'>Hello, world!</h1>
```

is just much more pleasant.

Ultimately, all the JSX code we write will get compiled down to standard
JavaScript and turn into things like `React.createElement`.

Every lab from now on already has the JSX transforming stuff set up for you. You
just need to run `npm install` and `npm start` to execute the JSX --> React
run-able JavaScript process.

## Resources

- JSX: https://facebook.github.io/react/docs/jsx-in-depth.html

<p class='util--hide'>View <a href='https://learn.co/lessons/react-jsx'>JSX</a> on Learn.co and start learning to code for free.</p>

[frag]: https://reactjs.org/docs/fragments.html
