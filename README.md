# RxJSX

This library is an experiment to use RxJS as a fine grained DOM renderer with [RxJS Autorun](https://github.com/kosich/rxjs-autorun).

It accomplishes this with using [Babel Plugin JSX DOM Expressions](https://github.com/ryansolid/babel-plugin-jsx-dom-expressions). It compiles JSX to DOM statements and wraps expressions in functions that can be called by the library of choice. In this case ko.computed wrap these expressions ensuring the view stays up to date. Unlike Virtual DOM only the changed nodes are affected and the whole tree is not re-rendered over and over.

To use include 'babel-plugin-jsx-dom-expressions' in your babelrc, webpack babel loader, or rollup babel plugin

```js
"plugins": [["jsx-dom-expressions", {moduleName: 'rxjsx'}]]
```

# Installation
```sh
> npm install rxjsx babel-plugin-jsx-dom-expressions
```

## API


For example:

```jsx
import { BehaviorSubject } from 'rxjs';
import { render, $ } from 'rxjsx'

const Greeter = (props) =>
  <div onClick={props.onClick}>Hello {props.name || 'World'}</div>

function App() {
  const name = new BehaviorSubject('John');
  return <>
    <h1>Greeting Example</h1>
    <Greeter name={$(name)} onClick={() => name.next('Jake')}/>
  </>;
}

render(App, document.getElementById('main'));
```

Control flow works the way you generally would JSX. However for performant list rendering I have added a fn on `subscribable` called `memoMap` that will optimally handle arrays.

```jsx
const list = ko.observableArray(["Alpha", "Beta", "Gamma"])

<ul>{
  list.memoMap(item => <li>{item}</li>)
}</ul>
```
### Example
[Counter](https://codesandbox.io/s/knockout-jsx-counter-dqtc2)

### Non-Precompiled

For those who do not wish to use Babel to precompile, the Knockout JSX supports Tagged Template Literals or HyperScript render APIs. These are available when you install the companion library and throw import of 'ko-jsx/html' and 'ko-jsx/h'. Refer to the docs on [Lit DOM Expressions](https://github.com/ryansolid/lit-dom-expressions), and [Hyper DOM Expressions](https://github.com/ryansolid/hyper-dom-expressions), respectively.

```js
import { BehaviorSubject } from 'rxjs';
import { h, $, render } from 'rxjsx/h';

const Greeter = props =>
  h('div', {onclick}, 'Hello', () => props.name || 'World');

function App() {
  const name = new BehaviorSubject('John');
  return h('div', [
    h('h1', 'Greeting Example'),
    h(Greeter, {name: () => $(name), onClick: () => name('Jake')})
  ]);
}

render(App, document.getElementById('main'));
```

