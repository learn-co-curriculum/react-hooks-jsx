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

#### JSX is _not_ a String

The JSX in the example is not wrapped in quotes. Think of it as another
type in JavaScript. **We are not interpolating HTML Strings** like we do with
standard JavaScript DOM manipulation.

#### JSX is the return value of the `render()` method

Every component you use needs a `render()` method that returns some valid JSX.
Although our example displays six lines of JSX, this is done for readability
only. The entire return statement is wrapped in parentheses so it is considered
one 'chunk' of JSX code, with _one_ top level element.

```js
return (
  <div  className="tweet">
    ...
  </div>
)
```

#### JSX Can Include JavaScript

While writing our pseudo-HTML in JSX, we can also write vanilla JavaScript
_in-line_. We do this by wrapping the JavaScript code in curly braces.

```js
<p>{ Math.floor(Math.random()*100) } retweets</p>
<p>{ this.currentTime() }</p>
```

In the example, we call the `Math.floor()`  and `Math.random()` methods
directly, which will return a random number when the component is rendered.

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

...we are _implicitly binding_ the method to the Tweet class. Getting acquainted with
using [arrow functions][arrowf] now will save headaches later.

As we get into _props_ in React, we sometimes need to call functions like
`currentTime()` in a class _different from its origin_. When this happens,
**without the arrow function**, we often have to explicitly bind methods to the
class their _originally from_, causing us to write code like this:

```js
this.currentTime().bind(this)
```

Without the `.bind(this)`, the _first_ 'this' will refer to whatever object it
is in when called, which can be a _different_ component.

#### JSX Cannot Include _All_ JavaScript Statements

JSX is an extension of JavaScript, wrapping a lot of underlying function calls
in a syntactically appealing style. This is why JSX code is considered
_declarative_. When _we're_ writing in JSX, it is equivalent to saying "Make an
h1 element with this content inside" and letting React work on the element
creation and function calls. Because we follow the proper syntax, React knows
that when we write:

```js
<h1 id="header">Hello!</h1>
```

The JSX needs to be converted into regular, imperative Javascript:

```js
React.createElement("h1", {id: "header"}, "Hello!")
```

Which is then committed to the actual DOM as an 'h1' DOM node.

Due to this, as well as JSX's specific syntax, we aren't able to include just
_any_ JavaScript statement. For instance, the following will not work in JSX:

```js
<h1 id="header">{if (true) {
  "Hello"
} else {
  "Goodbye"
}}</h1>
```

However, the ternary alternative _does_ work:

```js
<h1 id="header">{ true ? "Hello" : "Goodbye" }</h1>
```

There is an easy work around though - you can call class methods in JSX, and
within these methods, you can include whatever valid JavaScript you'd like.

#### A Component Must Return One JSX Element

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
exceptions to this, such as [React fragments][frag], but most
often, we will be using the HTML-like JSX elements.

## Avoiding Keywords

One thing to note about JSX is that, since we're still writing JavaScript code,
we need to avoid using keywords in our code. You might have noticed it already:
we're setting HTML classes using the `className` attribute (or prop, in React
terms), instead of `class`. This is because `class` is a reserved keyword in
JavaScript! The same thing is true for the `for` label, which is another keyword
in JS. If you want to use the HTML `for` attribute, you'd use `htmlFor` instead.

## Conclusion

In the early forms of React, instead of JSX, components returned JavaScript that
was much less reader friendly. To create a React element, we would write things
like this:

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

is just much more pleasant. When we're building complex applications, where
components can be children of other components, JSX provides a critical boost to
readability.

Ultimately, all the JSX code we write will get compiled down to standard
JavaScript and turn into things like `React.createElement`.

Every lab from now on already has the JSX transforming stuff set up for you. You
just need to run `npm install` and `npm start` to execute the JSX --> React
run-able JavaScript process.

## Resources

- JSX: https://facebook.github.io/react/docs/jsx-in-depth.html

<p class='util--hide'>View <a href='https://learn.co/lessons/react-jsx'>JSX</a> on Learn.co and start learning to code for free.</p>

[frag]: https://reactjs.org/docs/fragments.html
[arrowf]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions
