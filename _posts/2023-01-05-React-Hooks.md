---
title: React Hooks
date: 2023-01-05
categories: [React]
tags: [react, hooks] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

## What are Hooks?

Hooks are a new feature addition in React version 16.8 which allow you to use React features without having to write a class.

* Hooks don't work inside classes but your app can mix classes and functional components with them.

* Here you don't have to remember dealing with the "this" keyword or remember to bind event handlers.

* Without hooks there is no particular way to reuse stateful component logic. HOC and render props patterns do address this problem but makes the code harder to follow.

* Without hooks related code is not organized in one place. By the way completely unrelated code could end up in the same code block, for example, in componentDidMount:

Ex: Data fetching - In componentDidMount and componentDidUpdate.

Ex: Event listeners - In componentDidMount and componentWillUnmount.

Because of stateful login cannot break components into smaller ones. It'd better if all the related code was together...

* Use React version 16.8 or higher if you wanna use Hooks. Hooks are completely opt in.

* Hooks don't contain any breaking changes and the release is 100% backwards-compatible.

* Hooks don't replace existing knowledge of React concepts, instead, Hooks provide a more direct API to the React concepts you already know.

## useState Hook

* The useState Hook returns an array with 2 elements, the first element is the current value of the state, and the second element is a state setter function.

```react
import { useState } from "react";

function HookCounter() {

  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count {count}</button>
    </div>
  );
}

export default HookCounter;
```

* Only call hooks at the top level of your React function
* Don't call hooks inside loops, conditions or nested functions.

## useState with previous state

* New state value depends on the previous state value? You can pass a function to the setter function.

This seems safe but it is not the correct way to manage state:

```react
import { useState } from "react"

function HookCounterTwo() {

    const initialCount = 0
    const [count, setCount] = useState(initialCount)

  return (
    <div>Count: {count}
    <button onClick={() => setCount(initialCount)}>RESET</button>
    <button onClick={() => setCount(count + 1)}>INCREMENT</button>
    <button onClick={() => setCount(count - 1)}>DECREMENT</button>
    </div>
  )
}

export default HookCounterTwo
```
### What could go wrong?

This is gonna increment by 1 instead of 5:

```react
import { useState } from "react";

function HookCounterTwo() {
  const initialCount = 0;
  const [count, setCount] = useState(initialCount);

  const incrementFive = () => {
    for (let i = 0; i < 5; i++) {
      setCount(count + 1);
    }
  };

  return (
    <div>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>RESET</button>
      <button onClick={() => setCount(count + 1)}>INCREMENT</button>
      <button onClick={() => setCount(count - 1)}>DECREMENT</button>
      <button onClick={incrementFive}>INCREMENT 5</button>
    </div>
  );
}

export default HookCounterTwo;
```
* We could face unexpected results using this approach, better use this one:

```react
import { useState } from "react";

function HookCounterTwo() {
  const initialCount = 0;
  const [count, setCount] = useState(initialCount);

  const incrementFive = () => {
    for (let i = 0; i < 5; i++) {
      setCount((previousCount) => previousCount + 1);
    }
  };

  return (
    <div>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>RESET</button>
      <button onClick={() => setCount((previousCount) => previousCount + 1)}>
        INCREMENT
      </button>
      <button onClick={() => setCount((previousCount) => previousCount - 1)}>
        DECREMENT
      </button>
      <button onClick={incrementFive}>INCREMENT 5</button>
    </div>
  );
}

export default HookCounterTwo;
```

## useState with objects

useState doesn't automatically merge and update objects. This is a key difference to setState() which we come across in class components. You have to do the merge manually with the spread operator. Try don't using it and when you write in the input firsName will overwrite lastName and you won't be able to see both texts (notice in the following example the use of JSON.stringify() to help to see the difference).

Basically we're asking for copying every property in the name object and then just overwrite the firstName/lastName field with a different value.

* Make a replica of the name object and then only update the firstName/lastName property to the new value

```react
import { useState } from "react";

function HookCounterThree() {
  const [name, setName] = useState({
    firstName: "",
    lastName: "",
  });

  return (
    <form>
      <input
        type="text"
        value={name.firstName}
        onChange={(e) => setName({ ...name, firstName: e.target.value })}
      />

      <input
        type="text"
        value={name.lastName}
        onChange={(e) => setName({ ...name, lastName: e.target.value })}
      />

      <h2>Your first name is - {name.firstName}</h2>
      <h2>Your last name is - {name.lastName}</h2>
      <h2>{JSON.stringify(name)}</h2>
    </form>
  );
}

export default HookCounterThree;
```

## useState with arrays

In the following example, in order to add a new random number to the end of the list, we're in a similar situation as we were with objects. We have to copy the array using the spread operator and then add the item (which is an object) to the end.

```react
import { useState } from "react";

function HookCounterFour() {
  const [items, setItems] = useState([]);

  const addItem = () => {
    setItems([
      ...items,
      {
        id: items.length,
        value: Math.floor(Math.random() * 10) + 1,
      },
    ]);
  };

  return (
    <div>
      <button onClick={addItem}>ADD A NUMBER</button>
      <ul>
        {items.map((item) => (
          <li key={item.id}>{item.value}</li>
        ))}
      </ul>
    </div>
  );
}

export default HookCounterFour;
```

* WHEN DEALING WITH OBJECTS OR ARRAYS, ALWAYS MAKE SURE TO SPREAD YOUR STATE VARIABLE AND THEN USE THE SETTER FUNCTION.

## useEffect Hook

If you were working with class components you would have performed side-effects in your components, ex: updating the DOM, fetching data from an API endpoint and setting up subsciptions or timers. Since the render method would be too early to perform side-effects you had to make use of the life cycle methods. Ex: updating the document title to the current counter value, on initial render you wanna set the document title to clicked zero times so this code goes in componentDidMount which is executed only once in the component lifecycle we then would have a button to increment the count state value but when count value increments we also need to update the document title again and for that we add the same piece of code in componentDidUpdate lifecycle method. This lifecycle method is called anytime the component updates and is perfect for updating the document title.

```react
componentDidMount() {
  document.title = `You clicked ${this.state.count} times`;
}

componentDidUpdate() {
  document.title = `You clicked ${this.state.count} times`;
}
```

Another example with a timer:

```react
componentDidMount() {
  this.interval = setInterval(this.tick, 1000)
}

componentWillUnmount() {
  clearInterval(this.interval)
}
```

Combine the two side effects:

```react
componentDidMount() {
  document.title = `You clicked ${this.state.count} times`
  this.interval = setInterval(this.tick, 1000)
}

componentDidUpdate() {
  document.title = `You clicked ${this.state.count} times`;
}

componentWillUnmount() {
  clearInterval(this.interval)
}
```

Everything works as expected but you start to wonder if it could be better.

1. To update the document title we're writing the same code twice, once in componentDidMount() and once in componentDidUpdate().

2. The code is together and split apart? The code related to the timer, setInterval and cleanInterval, which are related are put into different code blocks, different lifecycle methods.

3. The code to update the DOM and setInterval are completely unrelated and are put together in componentDidMount().

Would be nice to not repeat code and group together only the related code. Here is where effect hook comes into picture.

* The useEffect hook lets you perform side effects in functional components while addressing the problems we just discussed.

* It is a close replacement for componentDidMount, componentDidUpdate and componentWillUnmount. useEffect can handle all of that.

## useEffect after render

```react
import { useState, useEffect } from "react";

function UseEffectDemo() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>
        CLICK {count} TIMES
      </button>
    </div>
  );
}

export default UseEffectDemo;
```

```react
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  // HERE useEffect RUNS AFTER EVERY RENDER OF THE COMPONENT,
  // after initial render and every subsequent render.
  // It's like being using componentDidMount and
  // componentDidUpdate at the same time.
```

## Run effects conditionally

In some cases applying the effect after every render might create performance problems, so we need a way to conditionally run an effect from a functional component.

In the following example we're condiotionally updating the title only when the appropriate variable changes, that's only when the count value changes

First with class component and then with functional component and the useEffect hook.

```react
import { Component } from "react";

export class ClassCounter extends Component {
  constructor(props) {
    super(props);

    this.state = {
      count: 0,
      name: "",
    };
  }

  componentDidMount() {
    document.title = `Clicked ${this.state.count} times`;
  }

  componentDidUpdate(prevProps, prevState) {
    // Without the conditional we'd be rendering on any input change
    if (prevState.count !== this.state.count) {
      console.log("Updating document title");
      document.title = `Clicked ${this.state.count} times`;
    }
  }

  render() {
    return (
      <div>
        <input
          type="text"
          value={this.state.name}
          onChange={(e) => {
            this.setState({ name: e.target.value });
          }}
        />
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click {this.state.count} times
        </button>
      </div>
    );
  }
}

export default ClassCounter;
```

```react
import { useState, useEffect } from "react";

function HookCounter() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");

  useEffect(() => {
    console.log(`useEffect - Updating document title`);
    document.title = `You clicked ${count} times`;
  }, [count]);

  return (
    <div>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <button onClick={() => setCount(count + 1)}>Count {count}</button>
    </div>
  );
}

export default HookCounter;
```

Without the "[count]" as the second parameter we'd be rendering on any input change. With it we're now only rendering when count changes.

* For conditionally executing an effect we pass in a second parameter, this parameter is an array. Within this array we need to specify either props or state that we need to watch for. Only if those props and states specified in this array were to change the effect would be executed.

## Run effects only once

First the example with class component and then with functional component and the useEffect hook.

```react
import { Component } from "react";

class ClassMouse extends Component {
  constructor(props) {
    super(props);

    this.state = {
      x: 0,
      y: 0,
    };
  }

  logMousePosition = (e) => {
    this.setState({ x: e.clientX, y: e.clientY });
  };

  componentDidMount() {
    window.addEventListener("mousemove", this.logMousePosition);
  }

  render() {
    return (
      <div>
        X - {this.state.x} Y - {this.state.y}
      </div>
    );
  }
}

export default ClassMouse;
```

```react
import { useState, useEffect } from "react";

function HookMouse() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);

  const logMousePosition = (e) => {
    console.log("Mouse event");
    setX(e.clientX);
    setY(e.clientY);
  };

  useEffect(() => {
    console.log("useEffect called");
    window.addEventListener("mousemove", logMousePosition);
  });

  return (
    <div>
      Hooks 💋 X - {x} Y - {y}
    </div>
  );
}

export default HookMouse;
```
When we move the mouse around the effect is called everytime the component re-renders (look at the logs in console), unless you specify a dependency array...

For our example we don't want the effect to depend on anything. We want it to be called once on initial render only and the way to achieve that is by simply specifying an empty array as the second parameter to useEffect.

We're telling React that this particular effect doesn't depend on any props or state. So there's no reason to call this effect on re-renders. Just render it on initial render, once...

Now only logMousePosition is called when moving the mouse but the code in userEffect just once at the beginning.

* This is how we mimicked componentDidMount.

## useEffect with cleanup

Now let's mimick componentWillUnmount !!

For this example I'm gonna create a container component for HookMouse which we implemented in the previous part "Run effects only once". In the container I'll simple add a button that will toggle the component visibility.

When you move the mouse you're gonna see the coordinates updating and the log statement is printed everytime the mouse moves.

If you click on the toggle display button, you'll unmount the component from the DOM but when you try moving the mouse around again, you're gonna see the mouse event is still active. The component has been removed but the event listener which belongs to that component is still listening and executing.

This is a memory leak. You have to cancel all subscriptions and listeners before removing components.

```react
import { useState } from "react";
import HookMouse from "./HookMouse";

function MouseContainer() {
  const [display, setDisplay] = useState(true);

  return (
    <div>
      <button onClick={() => setDisplay(!display)}>TOGGLE DISPLAY</button>
      {display && <HookMouse />}
    </div>
  );
}

export default MouseContainer;
```

If you'd be with class components you just have to use:

```react
componentWillUnmount() {
  window.removeEventListener("mousemove", this.logMousePosition)
}
```

To mimick this lifecycle functionality in useEffect we have to return a function in useEffect which will be executed when the component will unmount. So whatever you return is basically your clean up function.

```react
useEffect(() => {
    console.log("useEffect called");
    window.addEventListener("mousemove", logMousePosition);

    return () => {
      console.log("Component unmounting code");
      window.removeEventListener("mousemove", logMousePosition);
    };
  }, []);
```

## useEffect with incorrect dependency

I'm gonna create the typical timer with useEffect:

```react
import { useState, useEffect } from "react";

function IntervalHookCounter() {
  const [count, setCount] = useState(0);

  const tick = () => {
    setCount(count + 1);
  };

  useEffect(() => {
    const interval = setInterval(tick, 1000);

    return () => {
      clearInterval(interval);
    };
  }, []);

  return <h1>{count}</h1>;
}

export default IntervalHookCounter;
```

It looks pretty good but in the browser doesn't work 😥

If you think dependency array is a way to specify when you wanna re-run the effect there will be problems. Instead dependency array should be thought of as a way to let React know about everything that the effect must watch for changes.

So our mindset was to simply replicate componentDidMount however by specifying an empty array we have basically told React to ignore watching for changes in the count variable.

So React goes like hey on initial render the count value is 0 which implies setCount will set it to 0 + 1 which is 1 and I'll render that in the browser. Now you're telling me that I don't have to watch for changes in the count value. Count value is 1 right now and I'll just render that value through the different re-render cycles. If you want me to watch a variable just add it to the dependency array.

If we now add count as a dependency and take a look at the browser you can see we get the expected result.

So whenever you try to specify an empty dependency list please make sure that you really don't have any.

You could let the dependency array empty if you track the previous count in the tick function.

So you have to add "[count]" to the previous code or write the tick function like this:

```react
const tick = () => {
    setCount(prevCount => prevCount + 1);
  };
```

### Multiple useEffects

* If you have multiple effects to run make sure you to separate them out rather than having all the code in a single useEffect.

## Fetching data with useEffect

I could use the fetch API but I'm gonna use [Axios](!https://axios-http.com/), and [JSONPlaceholder](!https://jsonplaceholder.typicode.com/) as the API endpoint.

```bash
npm install axios
```

### Example 1

Notice the use of an empty dependency list to useEffect ("[]"). If we don't use it everything would seem working good but if we take a look at the console we have an infinite loop of data fetching but we want the data to be fetch only once on "ComponentDidMount" so we add the empty array.

```react
import { useState, useEffect } from "react";
import axios from "axios";

function DataFetching() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    axios
      .get("https://jsonplaceholder.typicode.com/posts")
      .then((response) => {
        console.log(response);
        setPosts(response.data);
      })
      .catch((error) => {
        console.log(error);
      });
  }, []);

  return (
    <div>
      <h1>Posts title</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default DataFetching;
```

### Example 2

Let's learn how to fetch an individual post by making the GET request on a input change.

Now the fetch render is gonna depend on "id", so we have to change the empty array by "[id]"

```react
import { useState, useEffect } from "react";
import axios from "axios";

function DataFetching() {
  const [post, setPost] = useState({});
  const [id, setId] = useState(1);

  useEffect(() => {
    axios
      .get(`https://jsonplaceholder.typicode.com/posts/${id}`)
      .then((response) => {
        console.log(response);
        setPost(response.data);
      })
      .catch((error) => {
        console.log(error);
      });
  }, [id]);

  return (
    <div>
      <input type="text" value={id} onChange={(e) => setId(e.target.value)} />
      <h1>Single post:</h1>
      <div>{post.title}</div>
    </div>
  );
}

export default DataFetching;
```

### Example 3

Now let's fetch an individual post by making the GET request on a button click.

```react
import { useState, useEffect } from "react";
import axios from "axios";

function DataFetching() {
  const [post, setPost] = useState({});
  const [id, setId] = useState(1);
  const [idFromButtonClick, setIdFromButtonClick] = useState(1);

  const handleClick = () => {
    setIdFromButtonClick(id);
  };

  useEffect(() => {
    axios
      .get(`https://jsonplaceholder.typicode.com/posts/${idFromButtonClick}`)
      .then((response) => {
        console.log(response);
        setPost(response.data);
      })
      .catch((error) => {
        console.log(error);
      });
  }, [idFromButtonClick]);

  return (
    <div>
      <input type="text" value={id} onChange={(e) => setId(e.target.value)} />
      <button onClick={handleClick}>FETCH POST</button>
      <h1>Single title post:</h1>
      <div>{post.title}</div>
    </div>
  );
}

export default DataFetching;
```

## useContext Hook

Consider a React app with a lot of components and levels of depth. If we wanna pass a prop, it has to go through many component, ex: comp A, comp B, to finally arrives comp C. Even if components A and B don't need the prop we have to send the prop through them to be able to pass it to components further down in the tree. Imagine if the component were to be nested five or ten levels deep, all the components in between would have to forward the prop 😞. This specially becomes a problem for certain type of props such as language prefferences, UIP and authenticated user which are pretty much required by many components in your app. What would be nice is if we could directly send data to the required component without having to manually drill down the props through every level of the component tree. This is where context comes into picture.

* Context provides a way to pass data through the component tree without having to pass props down manually at every level.

### Context before Hooks

(For a further explanation go to my previous post)

```react
// App.js
import React from "react";
import ComponentA from "./components/ComponentA";

export const UserContext = React.createContext();

function App() {
  return (
    <div className="App">
      <UserContext.Provider value={"Ben"}>
        <ComponentA />
      </UserContext.Provider>
    </div>
  );
}

export default App;
```

```react
// ComponentA.js
import ComponentB from "./ComponentB";

function ComponentA() {
  return (
    <div>
      <ComponentB />
    </div>
  );
}

export default ComponentA;
```

```react
// ComponentB.js
import ComponentC from "./ComponentC";

function ComponentB() {
  return (
    <div>
      <ComponentC />
    </div>
  );
}

export default ComponentB;
```

```react
// ComponentC.js
import { UserContext } from "../App";

function ComponentC() {
  return (
    <div>
      <UserContext.Consumer>
        {(user) => {
          return <div>User context value: {user}</div>;
        }}
      </UserContext.Consumer>
    </div>
  );
}

export default ComponentC;
```

This is great but it's a lil bit more verbose than we'd like to be. If we just have one context it'd still be fine but look at this mess when we consume more than one context:

```react
// App.js
import React from "react";
import ComponentA from "./components/ComponentA";

export const UserContext = React.createContext();
export const ChannelContext = React.createContext();

function App() {
  return (
    <div className="App">
      <UserContext.Provider value={"Ben"}>
        <ChannelContext.Provider value={"Superman"}>
          <ComponentA />
        </ChannelContext.Provider>
      </UserContext.Provider>
    </div>
  );
}

export default App;
```

```react
// ComponentC.js
import { UserContext, ChannelContext } from "../App";

function ComponentC() {
  return (
    <div>
      <UserContext.Consumer>
        {(user) => {
          return (
            <ChannelContext.Consumer>
              {(channel) => {
                return (
                  <div>
                    User context value: {user}, channel context value: {channel}
                  </div>
                );
              }}
            </ChannelContext.Consumer>
          );
        }}
      </UserContext.Consumer>
    </div>
  );
}

export default ComponentC;
```

There's so much nesting. Let's see how to consume multiple context values in a more convenient way.

### Context Hooks to the rescue

There are three simple steps to consume the context value:

1. Import useContext.
2. Import the necessary context.
3. Call the useContext function and pass the context as its argument.

* useContext returns the context value so assign the function call to a variable

```react
// ComponentC.js
import { useContext } from "react"; // step 1
import { UserContext, ChannelContext } from "../App"; // step 2

function ComponentC() {
  const user = useContext(UserContext); // step 3
  const channel = useContext(ChannelContext); // step 3

  return (
    <div>
      User context value: {user}, channel context value: {channel}
    </div>
  );
}

export default ComponentC;
```

## useReducer

* useReducer is a hook that is used for state management.

* It's an alternative to useState.

What's the difference? useState is built using useReducer... It's a more primitive hook compared to useState.

When to use useReducer vs useState? Before answering let's see examples.

* There is similarity between [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) and useReducer hook:

---- Reduce in JavaScript:

array.reduce(reducer, initialValue)

singleValue = reducer(accumulator, currentValue)

* Reduce method returns a single value

---- useReducer in React:

useReducer(reducer, initialState)

newState = reducer(currentState, action)

* The action parameter is what dictates the state transition from the current state to the new state.

* useReducer returns a pair of values, [newState, dispatch] (dispatch is used to specify the action)

### Simple state & action

1. Import useReducer.
2. Call useReducer passing in a reducer function and the initial state. The reducer function accepts the current state (count), an action and returns a new state depending on the action.
3. The call to useReducer returns a pair of values, the current value of the state (count) and the dispatch method which is capable of acccepting an action to execute the code specified in the render function. We use this dispatch method to dispatch the appropiate action based on what button the user clicks.

```react
import { useReducer } from "react"; // step 1

const initialState = 0;

const reducer = (state, action) => {
  switch (action) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    case "reset":
      return initialState;
    default:
      return state;
  }
};

function CounterOne() {
  const [count, dispatch] = useReducer(reducer, initialState); // step 2 and 3

  return (
    <div>
      <h1>Count - {count}</h1>
      <button onClick={() => dispatch("increment")}>INCREMENT</button>
      <button onClick={() => dispatch("decrement")}>DECREMENT</button>
      <button onClick={() => dispatch("reset")}>RESET</button>
    </div>
  );
}

export default CounterOne;
```

### Complex state & action

Now instead of making use of a simple state and action we're gonna be using a state object and an action object. This is also the pattern that is going to seem familiar with all the Redux users.

```react
import { useReducer } from "react";

const initialState = {
  firstCounter: 0,
};

const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return { firstCounter: state.firstCounter + 1 };
    case "decrement":
      return { firstCounter: state.firstCounter - 1 };
    case "reset":
      return initialState;
    default:
      return state;
  }
};

function CounterTwo() {
  const [count, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h1>Count - {count.firstCounter}</h1>
      <button onClick={() => dispatch({ type: "increment" })}>INCREMENT</button>
      <button onClick={() => dispatch({ type: "decrement" })}>DECREMENT</button>
      <button onClick={() => dispatch({ type: "reset" })}>RESET</button>
    </div>
  );
}

export default CounterTwo;
```

This approach could be helpful, for example, concerning the value by which we need to increment or decrement the counter. Right now you can see that the value is 1. What if you wanted to add two more buttons that could increment or decrement the value by 5. That's easy when the action is an object. Right now our action object has just one property which is "action.type". We can add a second property called value which should be the number to increment or decrement the counter. So for the existing buttons I add a value property set to 1. And now I'll also duplicate the two buttons but this time set the value property to five. Finally instead of the hard-coded value of "1" we specify "action.value" within reducer function.

```react
import { useReducer } from "react";

const initialState = {
  firstCounter: 0,
};

const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return { firstCounter: state.firstCounter + action.value };
    case "decrement":
      return { firstCounter: state.firstCounter - action.value };
    case "reset":
      return initialState;
    default:
      return state;
  }
};

function CounterTwo() {
  const [count, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h1>Count - {count.firstCounter}</h1>
      <button onClick={() => dispatch({ type: "increment", value: 1 })}>
        INCREMENT 1
      </button>
      <button onClick={() => dispatch({ type: "decrement", value: 1 })}>
        DECREMENT 1
      </button>
      <button onClick={() => dispatch({ type: "increment", value: 5 })}>
        INCREMENT 5
      </button>
      <button onClick={() => dispatch({ type: "decrement", value: 5 })}>
        DECREMENT 5
      </button>
      <button onClick={() => dispatch({ type: "reset" })}>RESET</button>
    </div>
  );
}

export default CounterTwo;
```

Another scenario is with state as an object. Suppose you wanted to maintain two different counters. That turns out to be really simple if your state is an object. You simply add another property to your state. Let's add "secondCounter" with the initial value of 10. To change the second counter value I'm gonna create two more switch cases, "increment2" and "decrement2, these are for the second counter. Now we have two properties in the state object we're changing only one at a time. To get the expected output we have to modify the return statement to merge the state properties and we have already seen how to do that using the spread operator so within the object we spread the state object and then overwrite the appropiate property. In the render function we can now add the JSX pertaining to the second counter

```react
import { useReducer } from "react";

const initialState = {
  firstCounter: 0,
  secondCounter: 10,
};

const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return { ...state, firstCounter: state.firstCounter + action.value };
    case "decrement":
      return { ...state, firstCounter: state.firstCounter - action.value };
    case "increment2":
      return { ...state, secondCounter: state.secondCounter + action.value };
    case "decrement2":
      return { ...state, secondCounter: state.secondCounter - action.value };
    case "reset":
      return initialState;
    default:
      return state;
  }
};

function CounterTwo() {
  const [count, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h1>First Counter - {count.firstCounter}</h1>

      <h1>Second Counter - {count.secondCounter}</h1>

      <div>
        <button onClick={() => dispatch({ type: "increment", value: 1 })}>
          INCREMENT 1
        </button>
        <button onClick={() => dispatch({ type: "decrement", value: 1 })}>
          DECREMENT 1
        </button>
        <button onClick={() => dispatch({ type: "increment", value: 5 })}>
          INCREMENT 5
        </button>
        <button onClick={() => dispatch({ type: "decrement", value: 5 })}>
          DECREMENT 5
        </button>
      </div>

      <div>
        <button onClick={() => dispatch({ type: "increment2", value: 1 })}>
          INCREMENT SECOND COUNTER
        </button>
        <button onClick={() => dispatch({ type: "decrement2", value: 1 })}>
          DECREMENT SECOND COUNTER
        </button>
      </div>

      <button onClick={() => dispatch({ type: "reset" })}>RESET</button>
    </div>
  );
}

export default CounterTwo;
```
We can maintain both the state and action as objects. By using action as an object you're able to pass additional data to the reducer function. By using state as an object we're able to keep track of multiple state variables.

This approach of maintaining multiple variables in a single state object is suited when dealing with global state but right now we're dealing with local state and when dealing with local state we have another way to keep track of multiple variables, let's take a look at that in the following section:

## Multiple useReducers

Previously we maintained a state object to track two different counters. To update the second counter we also had to create additional switch cases in the reducer function. This if fine but if we need two counters with the exact same state transition there is a much simpler alternative and that's to use multiple useReducer hooks.

```react
import { useReducer } from "react";

const initialState = 0;

const reducer = (state, action) => {
  switch (action) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    case "reset":
      return initialState;
    default:
      return state;
  }
};

function CounterThree() {
  const [count, dispatch] = useReducer(reducer, initialState);
  const [countTwo, dispatchTwo] = useReducer(reducer, initialState);

  return (
    <div>
      <div>
        <h1>Count - {count}</h1>
        <button onClick={() => dispatch("increment")}>INCREMENT</button>
        <button onClick={() => dispatch("decrement")}>DECREMENT</button>
        <button onClick={() => dispatch("reset")}>RESET</button>
      </div>
      <div>
        <h1>Count Two - {countTwo}</h1>
        <button onClick={() => dispatchTwo("increment")}>INCREMENT</button>
        <button onClick={() => dispatchTwo("decrement")}>DECREMENT</button>
        <button onClick={() => dispatchTwo("reset")}>RESET</button>
      </div>
    </div>
  );
}

export default CounterThree;
```
When dealing with multiple state variables that have the same state transitions it's a good idea to have multiple useReducers making use of the same reducer function. This will avoid the complecity of merging the state if it were to be an object and also prevent us from duplicating code in the reducer function like we have already seen in the component "counterTwo".

## useReducer with useContext

So far we have seen examples with userReducer for local state management, that is at the component level. But at some point you might wanna share the state between components, that is we might wanna work with global state.

We can manage global state by combining useReducer and useContext.

I have components A, B, C, D, E and F.

* Within B I nested D
* Within C I nested E
* Within E I nested F
* App.js only imports A, B and C

The goal is to maintain a count state in "App.js" and modify that state from components A, D and F.

* Two main steps:

1. Create a counter in "App.js" using the reducer hook
2. Provide and consume the counter context in the required components

```react
// App.js
import React, { useReducer } from "react";
import ComponentA from "./components/ComponentA";
import ComponentB from "./components/ComponentB";
import ComponentC from "./components/ComponentC";

export const CountContext = React.createContext();

const initialState = 0;

const reducer = (state, action) => {
  switch (action) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    case "reset":
      return initialState;
    default:
      return state;
  }
};

function App() {
  const [count, dispatch] = useReducer(reducer, initialState);

  return (
    <CountContext.Provider
      value={{ countState: count, countDispatch: dispatch }}
    >
      <div className="App">
        Count - {count}
        <ComponentA />
        <ComponentB />
        <ComponentC />
      </div>
    </CountContext.Provider>
  );
}

export default App;
```

```react
// ComponentA.js
import React, { useContext } from "react";
import { CountContext } from "../App";

function ComponentA() {
  const countContext = useContext(CountContext);
  return (
    <div>
      Component A - {countContext.countState}
      <button onClick={() => countContext.countDispatch("increment")}>
        INCREMENT
      </button>
      <button onClick={() => countContext.countDispatch("decrement")}>
        DECREMENT
      </button>
      <button onClick={() => countContext.countDispatch("reset")}>RESET</button>
    </div>
  );
}

export default ComponentA;
```

```react
// ComponentB.js
import ComponentD from "./ComponentC";

function ComponentB() {
  return (
    <div>
      <ComponentD />
    </div>
  );
}

export default ComponentB;
```

```react
// ComponentC.js
import ComponentF from "./ComponentF";

function ComponentC() {
  return (
    <div>
      <ComponentF />
    </div>
  );
}

export default ComponentC;
```

```react
// ComponentD.js
import React, { useContext } from "react";
import { CountContext } from "../App";

function ComponentD() {
  const countContext = useContext(CountContext);
  return (
    <div>
      Component D - {countContext.countState}
      <button onClick={() => countContext.countDispatch("increment")}>
        INCREMENT
      </button>
      <button onClick={() => countContext.countDispatch("decrement")}>
        DECREMENT
      </button>
      <button onClick={() => countContext.countDispatch("reset")}>RESET</button>
    </div>
  );
}

export default ComponentD;
```

```react
import ComponentF from "./ComponentF";

// ComponentE.js
function ComponentE() {
  return (
    <div>
      <ComponentF />
    </div>
  );
}

export default ComponentE;
```

```react
// ComponentF.js
import React, { useContext } from "react";
import { CountContext } from "../App";

function ComponentF() {
  const countContext = useContext(CountContext);
  return (
    <div>
      Component F - {countContext.countState}
      <button onClick={() => countContext.countDispatch("increment")}>
        INCREMENT
      </button>
      <button onClick={() => countContext.countDispatch("decrement")}>
        DECREMENT
      </button>
      <button onClick={() => countContext.countDispatch("reset")}>RESET</button>
    </div>
  );
}

export default ComponentF;
```

The special thing here is that the count value is being shared across the components. So you can change the same state from many components.

## Fetching data with useReducer

First the example without useReducer:

```react
import { useState, useEffect } from "react";
import axios from "axios";

function DataFetchingOne() {
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");
  const [post, setPost] = useState({});

  useEffect(() => {
    axios
      .get("https://jsonplaceholder.typicode.com/posts/1")
      .then((response) => {
        console.log(response);
        setLoading(false);
        setPost(response.data);
        setError("");
      })
      .catch((error) => {
        console.log(error);
        setLoading(false);
        setPost({});
        setError(`Something went wrong! ${error}`);
      });
  }, []);

  return (
    <div>
      {loading ? "LOADING" : post.title}
      {error ? error : null}
    </div>
  );
}

export default DataFetchingOne;
```
Now with useReducer:

```react
import { useReducer, useEffect } from "react";
import axios from "axios";

const initialState = {
  loading: true,
  error: "",
  post: {},
};

const reducer = (state, action) => {
  switch (action.type) {
    case "FETCH_SUCCESS":
      return {
        loading: false,
        post: action.payload,
        error: "",
      };
    case "FETCH_ERROR":
      return {
        loading: false,
        post: {},
        error: `Something went wrong! ${action.payload}`,
      };
    default:
      return state;
  }
};

function DataFetchingTwo() {
  const [state, dispatch] = useReducer(reducer, initialState);

  useEffect(() => {
    axios
      .get("https://jsonplaceholder.typicode.com/posts/1")
      .then((response) => {
        console.log(response);
        dispatch({ type: "FETCH_SUCCESS", payload: response.data });
      })
      .catch((error) => {
        console.log(error);
        dispatch({ type: "FETCH_ERROR", payload: error });
      });
  }, []);

  return (
    <div>
      {state.loading ? "LOADING" : state.post.title}
      {state.error ? state.error : null}
    </div>
  );
}

export default DataFetchingTwo;
```

## useState vs useReducer

When do I use one or the other?

* Depending on the type of state:

Use useState if:

You're trying to manage primitive types like Number, String, Boolean, etc. Ex: count value.

Use useReducer if:

You're trying to manage an object or array. Ex: person object with name, lastname and age.

* Depending on the number of state transitions:

Use useState if:

You're updating one or two variables.

Use useReducer if:

You're updating many more. Because it makes your state transitions predictable.

* Depending on how related state transitions are:

Use useState if:

No relation.

Use useReducer if:

Yes relation:

In the previous section we saw how to fetch data using the reducer hook. There were three state variables: loading, post and error. Tipically you'd wanna manage this with useState however useReducer would be better suited as the state transitions are all related or tied to a specific action. All the three variables are updated together either when the data is fetched succefully or when there's an error. By making use of useReducer your code is more predictable and maintainable. If everything were be managed by different places with useState becomes harder to reason about.

* Depending on the business logic for the state transition:

Use useState if:

No business logic.

Use useReducer if:

Complex business logic.

* Depending on local vs global state:

Use useState if:

Maintain local component state.

Use useReducer if:

Maintain global component state.

With useReducer we simply have to pass the dispatch method down the component tree. That dispatch method can update several variables based on the action type. With useState however we'd have to pass down multiple update functions, one for each state.

## useCallback Hook

Before starting with useCallback hook let's understand a bit about performance optimization.

I have a component called ParentComponent which is simply a container for other components. If you take a look at the JSX you can see that we have five components in total, three differents of which two are reused with different props.

```react
// App.js
import ParentComponent from "./components/ParentComponent";

function App() {
  return (
    <div>
      <ParentComponent />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import Title from "./Title";
import Count from "./Count";
import Button from "./Button";

function ParentComponent() {
  const [age, setAge] = useState(20);
  const [salary, setSalary] = useState(1000);

  const incrementAge = () => {
    setAge(age + 1);
  };

  const incrementSalary = () => {
    setSalary(salary + 500);
  };

  return (
    <div>
      <Title />
      <Count text="Age" count={age} />
      <Button handleClick={incrementAge}>INCREMENT AGE</Button>
      <Count text="Salary" count={salary} />
      <Button handleClick={incrementSalary}>INCREMENT SALARY</Button>
    </div>
  );
}

export default ParentComponent;
```

```react
//Title.js
function Title() {
  console.log("Rendering Title");
  return <h1>useCallback hook</h1>;
}

export default Title;
```

```react
import React from "react";

function Count({ text, count }) {
  console.log(`Rendering ${text}`);
  return (
    <div>
      {text} - {count}
    </div>
  );
}

export default Count;
```

```react
import React from "react";

function Button({ handleClick, children }) {
  console.log("Rendering button - ", children);
  return <button onClick={handleClick}>{children}</button>;
}

export default Button;
```
You might be thinking why do we need to break this down into five components, could not be all the JSX in the parent component itself an make it simpler? Yes, but it's written like this to help us understand about performance optimization and useCallback hook.

To get a better understanding of the performance I've added logs statements in Title, Count and Button components. The log statement is added just to indicate if the component is being render onto the screen. So in the browser I click in refresh and open Dev Tools to see the five log statements, one for each of the components being rendered. Now if I click on a button the component is re-render, EVERY component is re-render! This isn't a problem with few components with basic logic but imagine if there were more. This could provoke performance issues. The re-render should only happen to the specific components that need it.

* How do we optimize this?

React.memo, a higher-order component, will prevent a functional component from being re-render if its props or state don't change. Keep in mind React.memo has nothing to do with hooks.

So let's make use of that in all the three components, when exporting wrap your component with React.memo:

```react
// Change the default export in each component:
export default React.memo(Title);
export default React.memo(Count);
export default React.memo(Button);
```
If we test again the logs, we now have fewer logs but it's still not right. When, for example, we "incrementAge" the "incrementSalary" button is still being re-render.

* Why is this happening?

If we go back to the code the Title component has no props or state of its own and hence doesn't re-render when we increment the age or salary.

Next we have count and button related to age.

* Count accepts age as a prop.
* Button accepts incrementAge as a prop which is dependent on age.

So when click on "incrementAge" button, both of these components should re-render.

* Problem: incrementSalary button also re-renders.

(The count component for salary doesn't re-render).

1. This is because a new incrementSalary function is created each time the parent component re-renders.

2. When dealing with functions we always have to consider reference equality, even though two functions have the exact same behavior it doesn't mean they're equal to each other so the function before the re-render is different to the function after the re-render.

3. Since the function is a prop, React.memo sees that the prop has changed and won't prevent the re-render.

How do we tell React that there's no need to create a new incrementSalary function when we update the age? The answer is useCallback hook.

* What is useCallback?

  It's a hook that will return a memoized version of the callback function that only changes if one of the dependencies has changed. If we relate this to our example what it means is that the useCallback hook will cash the incrementSalary function and return that if salary is incremented, so if the salary does increment, that is if the dependency has changed, only then a new function will be returned.

* Why do we need useCallback?

  It's useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders.

* How to use it?
 1. Import useCallback
 2. Call useCallback

```react
import { useState, useCallback } from "react";
import Title from "./Title";
import Count from "./Count";
import Button from "./Button";

function ParentComponent() {
  const [age, setAge] = useState(20);
  const [salary, setSalary] = useState(1000);

  const incrementAge = useCallback(() => {
    setAge(age + 1);
  }, [age]);

  const incrementSalary = useCallback(() => {
    setSalary(salary + 500);
  }, [salary]);

  return (
    <div>
      <Title />
      <Count text="Age" count={age} />
      <Button handleClick={incrementAge}>INCREMENT AGE</Button>
      <Count text="Salary" count={salary} />
      <Button handleClick={incrementSalary}>INCREMENT SALARY</Button>
    </div>
  );
}

export default ParentComponent;
```
Now on initial load we have all five renders and when we use a button we can now see only the two components re-rendering!
We click on "INCREMENT AGE" and salary button is not render anymore! We successfully optimized all our components.

* Why not use the callback hook every single time? Is using callback all the time a good idea?

https://kentcdodds.com/blog/usememo-and-usecallback

## useMemo Hook

I'm gonna create two buttons. Depending on the value of the first button a span with the text "even" or "odd" appears. The function handling that logic also has an artificial delay. When I click the first button a delay of one or two seconds happens in the app as expected. The problem is when I click the second button I find the same delay and it shouldn't to be related with the second button.

Why is the second button delayed as well? Because everytime the state updates the component re-renders and when that happens the isEven function is called again.

We need a way to tell React not to recalculate certain values when unnecessary, specially when it takes a long time to compute...


```react
import { useState } from "react";

function Counter() {
  const [counterOne, setCounterOne] = useState(0);
  const [counterTwo, setCounterTwo] = useState(0);

  const incrementOne = () => {
    setCounterOne(counterOne + 1);
  };

  const incrementTwo = () => {
    setCounterTwo(counterTwo + 1);
  };

  const isEven = () => {
    let i = 0;
    while (i < 2000000000) i++;
    return counterOne % 2 === 0;
  };

  return (
    <div>
      <span>{isEven() ? " EVEN " : " ODD "}</span>
      <button onClick={incrementOne}>Count One - {counterOne}</button>
      <button onClick={incrementTwo}>Count Two - {counterTwo}</button>
    </div>
  );
}

export default Counter;
```
### What is useMemo?

useMemo is a hook that will only recompute the cached value when one of the dependencies has changed. This optimization heads to avoid expensive calculations on every render. The way useMemo works is very similar to how useCallback works.

1. Import useMemo
2. Within the component call useMemo hook

As the first argument we pass in the function whose return value needs to be cashed. In our example this would be the arrow function to calculate if a number is odd or even.

As the second parameter we need to specify the dependencies. Our function depends on the value of "counterOne". Whenever "counterOne" changes we're telling React to recompute the value and not use the cached value. So in the array specify "counterOne".

useMemo returns a cached value which we're gonna assign to the variable "isEven". Remove the parentheses from "isEven()" in the JSX. It isn't gonna be a function call because it now stores a value.

```react
import { useState, useMemo } from "react";

function Counter() {
  const [counterOne, setCounterOne] = useState(0);
  const [counterTwo, setCounterTwo] = useState(0);

  const incrementOne = () => {
    setCounterOne(counterOne + 1);
  };

  const incrementTwo = () => {
    setCounterTwo(counterTwo + 1);
  };

  const isEven = useMemo(() => {
    let i = 0;
    while (i < 2000000000) i++;
    return counterOne % 2 === 0;
  }, [counterOne]);

  return (
    <div>
      <span>{isEven ? " EVEN " : " ODD "}</span>
      <button onClick={incrementOne}>Count One - {counterOne}</button>
      <button onClick={incrementTwo}>Count Two - {counterTwo}</button>
    </div>
  );
}

export default Counter;
```
Now the second button doesn't delay 💫. This is because React is using the cached value of "isEven" function to display where the countOne is odd or even. Since the value never changed for counterOne there's no need to recompute this odd or even value. React will simply use the cached value from the previous render.

You might feel that useCallback and useMemo are very similar but useCallback caches the provided function instance itself whereas useMemo invokes the provided function and caches its result. So if you need to cash a function use useCallback and when you need to cash the result of an invoked function use useMemo.

## useRef Hook

It makes possible to access DOM nodes directly within functional components. We'll take a look at one of the most common use cases which is focusing a text input.

First we need to implement the componentDidMount behavior as we wanna focus on the input element only once and that's after the component has mounted.

We use for that the useEffect hook and within it we want logic to focus the input element. In order to do it we use the Ref hook.

1. Import useRef.
2. Create a Ref variable by calling useRef and passing in the initial value.
3. Attach it to the input element. Do it with the reserved ref attribute.
4. Call the focus method on the input element.

The current property comes from React. React will set the refs current property to the corresponding DOM node, hence this is how you actually access the input element.

```react
import { useEffect, useRef } from "react"; // step 1

function FocusInput() {
  const inputRef = useRef(null); // step 2

  useEffect(() => {
    inputRef.current.focus(); // step 4
  }, []);

  return (
    <div>
      {/*step 3*/}
      <input ref={inputRef} type="text" />
    </div>
  );
}

export default FocusInput;
```

Let's make another scenario. Let's create a timer that update every second and a button to clear it. First with class component:

```react
import { Component } from "react";

class ClassTimer extends Component {
  // instance field or class property which will point the interval timer:
  interval;

  constructor(props) {
    super(props);

    this.state = {
      timer: 0,
    };
  }

  componentDidMount() {
    this.interval = setInterval(() => {
      this.setState((prevState) => ({ timer: prevState.timer + 1 }));
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }

  render() {
    return (
      <div>
        Class Timer - {this.state.timer}
        <button onClick={() => clearInterval(this.interval)}>
          Clear Timer
        </button>
      </div>
    );
  }
}

export default ClassTimer;
```

Now, how do we do it but with functional component?

```react
import { useState, useEffect } from "react";

function HookTimer() {
  const [timer, setTimer] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setTimer((prevTimer) => prevTimer + 1);
    }, 1000);

    return () => {
      clearInterval(interval);
    };
  }, []);

  return (
    <div>
      Hook Timer - {timer}
      <button onClick={() => clearInterval(interval)}>CLEAR HOOK TIMER</button>
    </div>
  );
}

export default HookTimer;
```
This looks good but we get an error because "interval" is scoped only in the effect hook and we're trying to use it in the cleaner button. Here is where useRef comes to the rescue.

Although useRef can hold a reference to a DOM node using the ref attribute it can also be used to store any mutable value and what is great about useRef is that the value will persist through the re-renders while also not causing any additional renders when its value changes. So to solve the problem at hand we'll use the useRef hook.

1. Import useRef.
2. Within the component invoke useRef and assign it to a variable.
3. Change all the ocurrences of "interval" by "interval.current"

```react
import { useState, useEffect, useRef } from "react";

function HookTimer() {
  const [timer, setTimer] = useState(0);
  const intervalRef = useRef();

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setTimer((prevTimer) => prevTimer + 1);
    }, 1000);

    return () => {
      clearInterval(intervalRef.current);
    };
  }, []);

  return (
    <div>
      Hook Timer - {timer}
      <button onClick={() => clearInterval(intervalRef.current)}>
        CLEAR HOOK TIMER
      </button>
    </div>
  );
}

export default HookTimer;
```

Now everything works, this is another usage for useRef hook. It can be used to create a generic container which can hold a mutable value similar to instance properties on a class component. This generic container doesn't cause re-renders when the data stored changes. At the same time is also remembers the stored data even after other state variables caused a re-render of this component.

## Custom Hooks

* A Custom Hook is basically a JavaScript function whose name starts with "use".

* Can also call other Hooks if required.

Why do we want it?

* Share logic between two or more components. Alternative to HOCS and Render Props.

### Example

We're gonna create a custom hook that updates the document title.

1. First we're gonna create a counter and set the document title
as we normally would without any custom hook.

2. Second we're gonna see how to extract the logic into a custom hook

```react
import { useState, useEffect } from "react";

function DocTitleOne() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count ${count}`;
  }, [count]);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>COUNT - {count}</button>
    </div>
  );
}

export default DocTitleOne;
```

Let's suppose we'd wanna repeat that logic into multiple components. There would be a lot of repetition. Here is where a custom hook makes sense.

In our current example we can create a custom hook that extracts the logic of updating the document title. We can then reuse the same custom hook in different components.

I'm gonna create a new folder in "src" called "hooks" and there a file called "useDocumentTitle.js"

```react
import { useEffect } from "react";

function useDocumentTitle(count) {
  useEffect(() => {
    document.title = `Count ${count}`;
  }, [count]);
}

export default useDocumentTitle;
```

```react
import { useState } from "react";
import useDocumentTitle from "../hooks/useDocumentTitle";

function DocTitleOne() {
  const [count, setCount] = useState(0);

  useDocumentTitle(count)

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>COUNT - {count}</button>
    </div>
  );
}

export default DocTitleOne;
```

Now we are saving us from code repetition, instead of using the useEffect we're using the hook.

### Second Example

Let's make a counter custom hook! Create a new file in hooks directory called "useCounter.js"

This is how our component would look without reusing code:

```react
import { useState } from "react";

export default function CoolCounter() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount((prevCount) => prevCount + 1);
  };

  const decrement = () => {
    setCount((prevCount) => prevCount - 1);
  };

  const reset = () => {
    setCount(0);
  };

  return (
    <div>
      <h2>Count = {count}</h2>
      <button onClick={increment}>INCREMENT</button>
      <button onClick={decrement}>DECREMENT</button>
      <button onClick={reset}>RESET</button>
    </div>
  );
}
```

Here we reuse the code by using the useCounter hook:

```react
import { useState } from "react";

function useCounter(initialCount = 0, value) {
  const [count, setCount] = useState(initialCount);

  const increment = () => {
    setCount((prevCount) => prevCount + value);
  };
  const decrement = () => {
    setCount((prevCount) => prevCount - value);
  };
  const reset = () => {
    setCount(initialCount);
  };

  return [count, increment, decrement, reset];
}

export default useCounter;
```

```react
import useCounter from "../hooks/useCounter";

export default function CoolCounter() {

  const [count, increment, decrement, reset] = useCounter(5, 3);

  return (
    <div>
      <h2>Count = {count}</h2>
      <button onClick={increment}>INCREMENT</button>
      <button onClick={decrement}>DECREMENT</button>
      <button onClick={reset}>RESET</button>
    </div>
  );
}
```

We just copy-pasted the logic again. Because the useCounter hook is returning the values and methods in an array we can use array destructuring in the CoolCounter component, AWESOME!

Also you can specify a different initialCount (by default is 0, but I changed it to 5) and a value for incrementing and decrementing (I chose 3).

### Third Example

Let’s make an input custom hook! Create a new file in hooks directory called “useInput.js”

This is how our component would look without reusing code:

```react
import { useState } from "react";

function UserForm() {
  const [firstName, setFirstName] = useState("");
  const [lastName, setLastName] = useState("");

  const submitHandler = (e) => {
    e.preventDefault();
    alert(`Hello ${firstName} ${lastName}`);
  };

  return (
    <div>
      <form onSubmit={submitHandler}>
        <div>
          <label>First name</label>
          <input
            value={firstName}
            onChange={(e) => setFirstName(e.target.value)}
            type="text"
          />
        </div>
        <div>
          <label>Last name</label>
          <input
            value={lastName}
            onChange={(e) => setLastName(e.target.value)}
            type="text"
          />
        </div>
        <button type="submit">SUBMIT</button>
      </form>
    </div>
  );
}

export default UserForm;
```

Here we reuse the code by using the useInput hook:

```react
import { useState } from "react";

function useInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  const reset = () => {
    setValue(initialValue);
  };

  const bind = {
    value, // This is a short hand for "value: value"
    onChange: (e) => {
      setValue(e.target.value);
    },
  };

  return [value, bind, reset];
}

export default useInput;
```

```react
import useInput from "../hooks/useInput";

function UserForm() {
  const [firstName, bindFirstName, resetFirstName] = useInput("");
  const [lastName, bindLastName, resetLastName] = useInput("");

  const submitHandler = (e) => {
    e.preventDefault();
    alert(`Hello ${firstName} ${lastName}`);
    resetFirstName();
    resetLastName();
  };

  return (
    <div>
      <form onSubmit={submitHandler}>
        <div>
          <label>First name</label>
          <input {...bindFirstName} type="text" />
        </div>
        <div>
          <label>Last name</label>
          <input {...bindLastName} type="text" />
        </div>
        <button type="submit">SUBMIT</button>
      </form>
    </div>
  );
}

export default UserForm;
```
