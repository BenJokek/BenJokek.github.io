---
title: Render Props - React
date: 2023-01-05
categories: [React]
tags: [react, render-props] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

(This comes after HOC article)

This is another pattern for sharing code between react components. We are in a similar situation as with HOC, we wanna avoid repeated code.

In React is possible to use a prop whose value is a function to control what is actually rendered by a component. Render Props pattern is based on this.

* The term "render prop" refers to a technique for sharing code between React components using a prop whose value is a function.

```react
// App.js
import User from "./components/User";

function App() {
  return (
    <div className="App">
      <User render={(isLoggedIn) => (isLoggedIn ? "Ben" : "Guest")} />
    </div>
  );
}

export default App;
```

```react
//User.js
import { Component } from "react";

class User extends Component {
  render() {
    return <div>{this.props.render(false)}</div>;
  }
}

export default User;
```

Simulating the example with HOC we saw before I'm gonna create a "Counter" component that is gonna be our container component where we implement the common functionality and that is the state along with the "incrementCount" method.

```react
// App.js
import ClickCounterTwo from "./components/ClickCounterTwo";
import HoverCounterTwo from "./components/HoverCounterTwo";
import Counter from "./components/Counter";

function App() {
  return (
    <div className="App">

      <Counter>
        {(count, incrementCount) => (
          <ClickCounterTwo count={count} incrementCount={incrementCount} />
        )}
      </Counter>

      <Counter>
        {(count, incrementCount) => (
          <HoverCounterTwo count={count} incrementCount={incrementCount} />
        )}
      </Counter>
    </div>
  );
}

export default App;

```

```react
//Counter.js
import { Component } from "react";

class Counter extends Component {
  constructor(props) {
    super(props);

    this.state = {
      count: 0,
    };
  }

  incrementCount = () => {
    this.setState((prevState) => {
      return { count: prevState.count + 1 };
    });
  };

  render() {
    return (
      <div>{this.props.children(this.state.count, this.incrementCount)}</div>
    );
  }
}

export default Counter;
```

```react
//ClickCounterTwo.js
import { Component } from "react";

class ClickCounterTwo extends Component {
  render() {
    const { count, incrementCount } = this.props;

    return <button onClick={incrementCount}>CLICKED {count} TIMES</button>;
  }
}

export default ClickCounterTwo;
```

```react
//HoverCounterTwo.js
import { Component } from "react";

class HoverCounterTwo extends Component {
  render() {
    const { count, incrementCount } = this.props;

    return <h2 onMouseOver={incrementCount}>HOVERED {count} TIMES</h2>;
  }
}

export default HoverCounterTwo;
```
