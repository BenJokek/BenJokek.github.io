---
title: Higher Order Components (HOC) - React
date: 2023-01-05
categories: [React]
tags: [react, hoc] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

Let's suppose we want to create three components, one that renders a button and has a state for counting clicks, another that is an <h2> and counts when we hover over it and the third is another element that increments its state per key stroke.

```react
// clickCounter.js
import { Component } from "react";

class ClickCounter extends Component {
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
    const { count } = this.state;
    return <button onClick={this.incrementCount}>CLICKED {count} TIMES</button>;
  }
}

export default ClickCounter;
```

```react
// HoverCounter.js
import { Component } from "react";

class HoverCounter extends Component {
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
    const { count } = this.state;
    return <h2 onMouseOver={this.incrementCount}>HOVERED {count} TIMES</h2>;
  }
}

export default HoverCounter;
```

Before keep creating components here, we can see we're repeating a lot of code. The counter functionality is being duplicated and it shouldn't.

### How can we reuse this code?

The immediate thought is to lift this state to the parent component and pass down the handler as a prop so define the counter functionality in app component and provide the state and handler as props to "ClickCounter" and "HoverCounter". This would work in our scenario where we have the counter components as children of the same parent but imagine a scenario where the counter components are scattered in the react component tree, lifting the state wouldn't be the correct solution. So there's a need to share common functionality between components without repeating code and that's where the concept of "Higher Order Components" comes into picture.

### What is a Higher Order Component?

* A pattern where a function takes a component as an argument and returns a new component.

const NewComponent = higherOrderComponent(originalComponent)

Typically an HOC adds additional data or functionality to the original component so the new component can also be reffered to as enhanced component.

const EnhancedComponent = higherOrderComponent(originalComponent)

const IronMan = withSuit(TonyStark)

### Implementation

```react
// App.js
import ClickCounter from "./components/ClickCounter";
import HoverCounter from "./components/HoverCounter";

function App() {
  return (
    <div className="App">
      <ClickCounter />
      <HoverCounter />
    </div>
  );
}

export default App;
```

```react
//withCounter.js
import { Component } from "react";

const withCounter = (WrappedComponent) => {
  class WithCounter extends Component {
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
        <WrappedComponent
          count={this.state.count}
          incrementCount={this.incrementCount}
        />
      );
    }
  }
  return WithCounter;
};

export default withCounter;
```

```react
//ClickCounter.js
import { Component } from "react";
import withCounter from "./withCounter";

class ClickCounter extends Component {
  render() {
    const { count, incrementCount } = this.props;

    return <button onClick={incrementCount}>CLICKED {count} TIMES</button>;
  }
}

export default withCounter(ClickCounter);
```

```react
//HoverCounter.js
import { Component } from "react";
import withCounter from "./withCounter";

class HoverCounter extends Component {
  render() {
    const { count, incrementCount } = this.props;

    return <h2 onMouseOver={incrementCount}>HOVERED {count} TIMES</h2>;
  }
}

export default withCounter(HoverCounter);
```

* Instead of exporting the ClickCounter or the HoverCounter component, we export the higher order component. Now we are reusing code avoiding repetition.

* If you try to pass props to the components from App.js you will encounter it doesn't work. That's because the props are passed to the HOC (withCounter.js). To fix this you need to pass down the remaining props to the "WrappedComponent" using the spread operator, so HOC adds the props to the App component and then simply passes down whatever remaining props have been specified. In our case it passes down the name prop. So when you create HOCs make sure to pass down the rest of the props.

```react
// App.js
import ClickCounter from "./components/ClickCounter";
import HoverCounter from "./components/HoverCounter";

function App() {
  return (
    <div className="App">
      <ClickCounter name="Ben"/>
      <HoverCounter />
    </div>
  );
}

export default App;
```

```react
//withCounter.js
import { Component } from "react";

const withCounter = (WrappedComponent) => {
  class WithCounter extends Component {
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
        <WrappedComponent
          count={this.state.count}
          incrementCount={this.incrementCount}
          {...this.props }
        />
      );
    }
  }
  return WithCounter;
};

export default withCounter;
```

```react
// clickCounter.js
import { Component } from "react";
import withCounter from "./withCounter";

class ClickCounter extends Component {
  render() {
    const { count, incrementCount } = this.props;

    return (
      <button onClick={incrementCount}>
        {this.props.name} CLICKED {count} TIMES
      </button>
    );
  }
}

export default withCounter(ClickCounter);
```

* The second thing to discuss is passing parameters to the HOC function. In our HOC let's say instead of incrementing the count value by 1, I wanna increment it by different numbers for both the counter components. We can do that by passing a parameter to the HOC function. So the arrow function will now have two parameters, the first one still "WrappedComponent" and the second one "incrementNumber". Instead of incrementing the count by one, we'll now increment it by the "incrementNumber" parameter.

```react
//withCounter.js
import { Component } from "react";

// Add the second parameter (incrementNumber):
const withCounter = (WrappedComponent, incrementNumber) => {
  class WithCounter extends Component {
    constructor(props) {
      super(props);

      this.state = {
        count: 0,
      };
    }

    // We use the second parameter here:
    incrementCount = () => {
      this.setState((prevState) => {
        return { count: prevState.count + incrementNumber };
      });
    };

    render() {
      return (
        <WrappedComponent
          count={this.state.count}
          incrementCount={this.incrementCount}
          {...this.props}
        />
      );
    }
  }
  return WithCounter;
};

export default withCounter;
```

```react
// clickCounter.js
import { Component } from "react";
import withCounter from "./withCounter";

class ClickCounter extends Component {
  render() {
    const { count, incrementCount } = this.props;

    return (
      <button onClick={incrementCount}>
        {this.props.name} CLICKED {count} TIMES
      </button>
    );
  }
}

export default withCounter(ClickCounter, 5); //Add second parameter
```

Now we're incrementing by 5 per click!!!
