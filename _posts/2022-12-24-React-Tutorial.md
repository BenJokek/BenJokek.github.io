---
title: React Tutorial
date: 2022-12-24
categories: [React]
tags: [react] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

## What is React?

React is an Open Source library for building user interfaces.

- Not a framework
- Focus on UI (not routing or http requests)
- Maintained by Facebook
- Most popular
- Component based architecture
- Big ecosystem and React Native for mobile applications

## Prerequisites

- I recommend knowing good HTML, CSS and JavaScript (ES6) in order to learn React.
- Nodejs installed

To create a new React App I am gonna use Create React App (a command line interface tool to quickly create and run React apps with no build configuration):

```bash
npx create-react-app my-react-tutorial
cd my-react-tutorial
npm start
```

I use npx (npm package runner installed with nodejs), it is better than having to install the package globally with npm and then having to update Create React App package.

## Folder Structure

![img-description](/reactAppContent.png)

### package.json

This file contains the dependencies and scripts require for the project. For example, you can see your React version ther listed as dependencies. Also scripts for start your app (ex. calling "npm start"), etc.

### package-lock.json

Based on whether you have npm or yarn as package manager you are going to see "package-lock.json" or "yarn-lock.json". They ensure consistent installation of your dependencies and you don't have to worry about them.

### node_modules

It is the folder where all the dependencies are installed. It is generated when you run the Create React App command or when you run npm install.

### public

This folder contains "manifest.json" that is concerned with progressive web apps which is out of scope here. The most important is the HTML file. It is going to be the only HTML file you are going to have in your application, we are working with single page apps. The view is going to dynamically change in the browser. You could change things in the head tag but rarely touch the body tag. You want React to control the UI and for that purpose we have one div tag with ID equal to root. At run time the React app takes over this div tag and is responsible for the UI. So, take note of the <div id="root"></div>.

### src

We are here most of the time. The starting point is "index.js". There we specify the root component which is App component and the DOM element wich will be controlled by React. The DOM element here is an element with an ID of root (the element we just mention from the HTML file). So our app, the App component, is rendered inside the Root DOM node (the div with id of root).
The App component is in the file "App.js". This is responsible for the HTML displayed in the browser.
"App.css" is applied to the App component and "index.css" applies styles to the body tag.
"serviceWorker.js" is related to proggresive web apps.

## Components

- Components are independent and reusable bits of code.
- Can be nested inside other components
- Can optionally receive an object of properties (props)
- Can maintain a private internal state which is information available to describe the UI
- Return something very similar to HTML (JSX).
- Two ways of making components:

### Class components

```react
import { Component } from "react";

class HelloWorld extends Component {
  render() {
    return <h1>Hello, World!</h1>;
  }
}
```

### Functional components

(just JavaScript functions)

```react
function HelloWorld() {
  return <h1>Hello, World!</h1>
}
```

Example

I am going to create a new folder called "components" in the "src" directory. Then I create my first function component "Greet.js" (PascalCase important). After created I have to connect it with my app, so I import "Greet.js" in "App.js" and I use it there by specifying it as a custom self-closing HTML tag.

I'm gonna use ES6 arrow function syntax for this component.

```react
// Greet.js
// function Greet() {
//  return <h1>Hello Ben!</h1>
// }

const Greet = () => <h1>Hello Ben!</h1>

export default Greet
```

```react
// App.js
import Greet from './components/Greet';

function App() {
  return (
    <div className="App">
      <Greet />
    </div>
  );
}

export default App;
```

### Default and Named Export

I'm using default export because I'm only exporting one thing. When you use "export default" you don't have to necesarily use the same name when importing.
Also there are named exports. If you use named export you have to import it respectant to names and those names (in this case exactly Greet) have to be between curly braces {}.

- Default export:

```react
const Greet = () => <h1>Hello Ben!</h1>
export default Greet
```

```react
import Greet from './components/Greet'
```

or

```react
import MyGreetComponent from './components/Greet'
```

- Named export:

```react
export const Greet = () => <h1>Hello Ben!</h1>
```

```react
import { Greet } from './components/Greet'
```

## JSX

- JavaScript XML (JSX) - Extension to the JavaScript language syntax
- Write XML-like code for elements and components
- JSX tags have a tag name, attributes, and children
- JSX is not a necessity to write React applications
- JSX makes your react code simpler and elegant
- JSX ultimately transpiles to pure JavaScript wich is understood by the browsers

### JSX example

```react
const Greet = () => <h1 id="greeting" className="dummyClass">Hello Ben!</h1>

export default Hello
```

### Without JSX example

```react
import React from "react";

const Hello = () =>
  React.createElement(
    "div",
    null,
    React.createElement(
      "h1",
      { id: "greeting", className: "dummyClass" },
      "Hello Ben!"
    )
  );

export default Hello;
```

The components above are the same, using JSX is just syntactic sugar.

React.createElement accepts three parameters React.

React.createElement(type, props, ...children)

The first parameter is a string wich specify the HTML tag to be render.

The second parameter is any optional props. The props argument must either be an object or null.

The third parameter is optional. It is the content of our element.

### JavaScript reserved keywords in JSX

We used className instead of just class because class is a reserved keyword in JavaScript. You are going to be alerted with useful errors when this happens again with other keywords. Just remember some words are reserved.

- class -> className
- for -> htmlFor

camelCase property naming convention:

- onclick -> onClick
- tabindex -> tabIndex

## Props

Props (properties) are optional inputs your components can accept to pass data. They are read-only and cannot be modified.

- Props get passed to the component as parameters
- Are inmutable
- Can be accessed using:

props - Functional Components

this.props - Class Components

```react
// App.js
import Greet from "./components/GreetFunction";

function App() {
  return (
    <div className="App">
      <Greet name="Ben" position="first">
        <p>This is children props</p>
      </Greet>
      <Greet name="Rick" position="second">
        <button>Click</button>
      </Greet>
      <Greet name="Bob" position="third" />
    </div>
  );
}

export default App;
```

```react
// GreetClass.js (Class Component version)
import { Component } from "react";

class GreetClass extends Component {
  render() {
    return (
      <>
        {console.log(props)}
        <h1>
          Hello {this.props.name}! You are {this.props.position}
        </h1>
        {this.props.children}
      </>
    );
  }
}

export default GreetClass;
```

```react
// GreetFunction.js (Functional Component version)
const GreetFunction = (props) => {
  return (
    <>
      {console.log(props)}
      <h1>
        Hello {props.name}! You are {props.position}
      </h1>
      {props.children}
    </>
  );
};

export default GreetFunction;
```

![img-description](/props.png)

Parenthesis are used to group multiline of codes on return statement so to prevent semicolon inserted automatically in the wrong place.

In the return has to be one element wrapping the rest when dealing with multiple elements, that's because I used the "empty elements" or Fragments (<> and </>). I could use divs there but I preferred using that in order to not adding extra nodes to the DOM.

With the console.log() I wrote, we can see in the browser console the prop objects we passed.

Curly braces { } are special syntax in JSX. It is used to evaluate a JavaScript expression during compilation. A JavaScript expression can be a variable, function, an object, or any code that resolves into a value.

In JSX expressions that contain both an opening tag and a closing tag, the content between those tags is passed as a special prop: props.children.

So as you can see props are passed from App.js to the components as objects to be positioned.

For properties you know for sure are going to be passed, you can passed them as attributes. If you have no clue what is going to be passed as props or you have to pass in dynamic HTML content, pass it in between the component tags and in the component definition simply render the content using "props.children". If you don't pass anything there simply it doesn't render anything.

## State

We already saw how to influence what is rendered on the screen using props. Now there is a second way to do it, the state of the component.

- State is managed within the component.
- Can be changed
- Can be accessed using:

useState Hook - Functional Components

this.state - Class Components

### Message Example

```react
// App.js
import Message from "./components/MessageClass";

function App() {
  return (
    <div className="App">
      <Message />
    </div>
  );
}

export default App;
```

```react
// MessageClass.js
import { Component } from "react";

class MessageClass extends Component {
  constructor() {
    super();
    this.state = {
      message: "Welcome visitor",
    };
  }

  handleClick = () => {
    this.setState({
      message: "Thank you for subscribing"
    });
  };

  render() {
    return (
      <div>
        <h1>{this.state.message}</h1>
        <button onClick={this.handleClick}>Subscribe</button>
      </div>
    );
  }
}

export default MessageClass;
```

```react
// MessageFunction.js
import { useState } from "react";

const MessageFunction = () => {
  const [message, setMessage] = useState("Welcome visitor");

  const handleClick = () => {
    setMessage("Thank you for subscribing");
  };

  return (
    <div>
      <h1>{message}</h1>
      <button onClick={handleClick}>Subscribe</button>
    </div>
  );
};

export default MessageFunction;
```

### Counter Example

This is gonna create a counter that increments a value depending on what is passed as props. If you pass a 5, it is gonna be incremented by 5 per click.

```react
// App.js
import Counter from "./components/CounterClass";

function App() {
  return (
    <div className="App">
      <Counter increment={1} />
    </div>
  );
}

export default App;
```

```react
// CounterClass.js
import { Component } from "react";

class Counter extends Component {
  constructor() {
    super();
    this.state = {
      count: 0,
    };
  }

  increment = () => {
    this.setState(
      (prevState, props) => ({
        count: prevState.count + props.increment,
      }),
      () => {
        console.log(
          "This code has to be executed after changing state",
          `You clicked ${this.state.count} times`
        );
      }
    );
    console.log(
      "If you try here, unexpected result could happens",
      `You clicked ${this.state.count} times`
    );
  };

  render() {
    return (
      <div>
        <h1>{this.state.count}</h1>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}

export default Counter;

```

- Always make use of setState and never modify the state directly.
- Code has to be executed after the state has been updated? Place that code in the call back function wich is the second argument to the setState method.
- When you have to update state based on the previous state value, pass in a function as an argument instead of the regular object.

Using Functional Components looks kinda simpler:

```react
// CounterFunction.js
import { useEffect, useState } from "react";

const CounterFunction = (props) => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("This code has to be executed after changing state", `You clicked ${count} times`);
  }, [count]);

  const increment = () => {
    setCount(count + props.increment);
  };

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={increment}>Increment</button>
    </div>
  );
};

export default CounterFunction;
```

## Destructuring props and state

- Destructuring is an ES6 feature
- Makes it possible to unpack values from arrays or objects into distinct variables.
- Improves code readibility

### Without Destructuring

```react
// GreetFunction.js

const GreetFunction = (props) => {
  return (
    <>
      <h1>
        Hello {props.name}! You are {props.position}
      </h1>
      {props.children}
    </>
  );
};

export default GreetFunction;
```

### With Destructuring 1

You can extract the values from the props object:

```react
// GreetFunction.js

const GreetFunction = ({name, position, children}) => {
  return (
    <>
      <h1>
        Hello {name}! You are {position}
      </h1>
      {children}
    </>
  );
};

export default GreetFunction;
```

### With Destructuring 2

You can also destructure in the function body:

```react
// GreetFunction.js

const GreetFunction = (props) => {
  const { name, position, children } = props;

  return (
    <>
      <h1>
        Hello {name}! You are {position}
      </h1>
      {children}
    </>
  );
};

export default GreetFunction;
```

### With Destructuring 3

Now an example with a Class Component. You can do the same with states:

```react
// GreetClass.js
import { Component } from "react";

class GreetClass extends Component {
  render() {

    const {name, position, children} = this.props

    const {state1, state2} = this.state
    // with states if I had some...

    return (
      <>
        <h1>
          Hello {name}! You are {position}
        </h1>
        {children}
      </>
    );
  }
}

export default GreetClass;
```

## Event Handling

I'm gonna focus just in click event but the concept holds good for other events aswell. Pay attention I use camelCase in "onClick", not "onclick", and I don't use "()" when referring to the clickHandler function.

```react
// FunctionClick.js
const FunctionClick = () => {

  const clickHandler = () => console.log("Button clicked")

  return <button onClick={clickHandler}>Click</button>
};

export default FunctionClick;
```

```react
// ClassClick.js
import { Component } from "react";

class ClassClick extends Component {
  constructor(props) {
    super(props);
    this.state = {
      message: "Hello!",
    };
    // this.clickHandler = this.clickHandler.bind(this);
  }
  // Common method and binding in constructor, this is the second best:
  //   clickHandler() {
  //     this.setState({
  //         message: "Goodbye!",
  //       });
  //   }

  // The best for me:
  clickHandler = () => {
    this.setState({
      message: "Goodbye!",
    });
  };

  render() {
    return (
      <>
        <h1>this.state.message</h1>
        <button onClick={this.clickHandler}>Click</button>
        {/* Could be bad for performance: */}
        {/* return <button onClick={this.clickHandler.bind(this)}>Click</button>*/}
        {/* return <button onClick={() => this.clickHandler()}>Click</button>*/}
      </>
    );
  }
}

export default ClassClick;
```

You can see I like using arrow functions, sometimes save us from errors and look better for me. If you try to use another syntax you could encounter binding errors. This is because how JavaScript "this" keyword works. There are other ways to sort this but this is my favourite. Search "Binding Event Handlers" in Google for further info. In React Docs https://reactjs.org/docs/handling-events.html they bind "this" in the constructor. Other approaches could be bad for performance.

## Methods as props

```react
// App.js
import ParentComponent from "./components/ParentComponent";

function App() {
  return (
    <div className="App">
      <ParentComponent />
    </div>
  );
}

export default App;
```

```react
// ParentComponent.js
import { Component } from "react";
import ChildComponent from "./ChildComponent";

class ParentComponent extends Component {

  greetParent = (childName) => alert(`Hello from ${childName}`);

  render() {
    return <ChildComponent greetHandler={this.greetParent} />;
  }
}

export default ParentComponent;
```

```react
// ChildComponent.js
// Use arrow function syntax when you wanna pass a parameter
const ChildComponent = ({ greetHandler }) => {
  return <button onClick={() => greetHandler("John")}>Greet Parent</button>;
};

export default ChildComponent;
```

## Conditional Rendering

### if/else

Adding if/else statements within JSX is not valid. Instead you can use it outside and then return the JSX.

```react
import { Component } from "react";

class UserGreeting extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isLoggedIn: false,
    };
  }
  render() {
    if (this.state.isLoggedIn) {
      return <h1>Welcome Ben</h1>;
    } else {
      return <h1>Welcome Guest</h1>;
    }
  }
}

export default UserGreeting;
```

### Element variables

Using JavaScript variables to store elements also can work:

```react
import { Component } from "react";

class UserGreeting extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isLoggedIn: true,
    };
  }
  render() {
    let message;

    if (this.state.isLoggedIn) {
      message = <h1>Welcome Ben</h1>;
    } else {
      message = <h1>Welcome Guest</h1>;
    }

    return message;
  }
}

export default UserGreeting;
```

### Ternary Conditional Operator

This is getting better

```react
import { Component } from "react";

class UserGreeting extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isLoggedIn: false,
    };
  }
  render() {
    return this.state.isLoggedIn ? (
      <h1>Welcome Ben</h1>
    ) : (
      <h1>Welcome Guest</h1>
    );
  }
}

export default UserGreeting;
```

### Short Circuit Operator

```react
import { Component } from "react";

class UserGreeting extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isLoggedIn: true,
    };
  }
  render() {
    return this.state.isLoggedIn && <div>Welcome Ben</div>;
    {/*it renders if true, empty if false*/}
  }
}

export default UserGreeting;
```

## List Rendering

### Array.map()

```react
const NameList = () => {
  const names = ["Ben", "Bob", "Juan"];
  return (
    <div>
      {names.map((name) => (
        <h2>{name}</h2>
      ))}
    </div>
  );
};

export default NameList;
```

Or in order to maintain the return clean:

```react
const NameList = () => {
  const names = ["Ben", "Bob", "Juan"];
  const nameList = names.map((name) => <h2>{name}</h2>);
  return <div>{nameList}</div>;
};

export default NameList;
```

Example with an object of people:

```react
const NameList = () => {
  const person = [
    {
      id: 1,
      name: "Ben",
      age: 28,
      skill: "Cobol",
    },
    {
      id: 2,
      name: "Bob",
      age: 29,
      skill: "React",
    },
    {
      id: 3,
      name: "Juan",
      age: 30,
      skill: "Angular",
    },
  ];
  const personList = person.map((person) => (
    <h2>
      I am {person.name}. I'm {person.age} years old. I know {person.skill}
    </h2>
  ));
  return <div>{personList}</div>;
};

export default NameList;
```

### Refactoring into separate components

The pattern you'll commonly see when building apps that render lists of data:

```react
import PersonList from "./components/PersonList";

function App() {
  return (
    <div className="App">
      <PersonList />
    </div>
  );
}

export default App;
```

```react
// PersonList.js
import Person from "./Person";

const PersonList = () => {
  const persons = [
    {
      id: 1,
      name: "Ben",
      age: 28,
      skill: "Cobol",
    },
    {
      id: 2,
      name: "Bob",
      age: 29,
      skill: "React",
    },
    {
      id: 3,
      name: "Juan",
      age: 30,
      skill: "Angular",
    },
  ];
  const personList = persons.map((person) => <Person person={person} />);
  return <div>{personList}</div>;
};

export default NameList;
```

```react
// Person.js
const Person = ({ person }) => {
  return (
    <h2>
      I am {person.name}. I'm {person.age} years old. I know {person.skill}
    </h2>
  );
};

export default Person;
```

Everything seem to be okay but if you open the console you'll see an error:

> Warning: Each child in a list should have a unique "key" prop.
{: .prompt-danger }

## Lists and Keys

- A "key" is a special string attribute you need to include when creating lists of elements
- Keys give the elements a stable identity
- Keys help React identify which items have changed, added or removed. So we get a more efficient update of the user interface

```react
// App.js
import NameList from "./components/NameList";

function App() {
  return (
    <div className="App">
      <NameList />
    </div>
  );
}

export default App;
```

```react
// NameList.js
import Name from "./Name";

const NameList = () => {
  const names = [
    {
      id: 1,
      name: "Ben",
    },
    {
      id: 2,
      name: "Bob",
    },
    {
      id: 3,
      name: "Juan",
    },
  ];

  // We have to pass a unique value, ideally an id, as a key prop.
  // Without refactoring would be <h2 key={name.id}></h2>
  // key prop is reserved so you can't use it in the child component if you try.

  const nameList = names.map((name) => <Name key={name.id} name={name} />);
  return <div>{nameList}</div>;
};

export default NameList;
```

```react
// Name.js
const Name = ({ name }) => {
  return <h2>I am {name.name}</h2>;
};

export default Name;
```

### Explanation

- List without key attribute

Let's suppose we have a list with two names and we need to add another one at the end.

```react
  <ul>
    <li>Ben</li>
    <li>Bob</li>
  <ul>
  // add name:
  <ul>
    <li>Ben</li>
    <li>Bob</li>
    <li>Juan</li>
  <ul>
```

TARGET: rendering only new items.

React iterates over both lists at the same time and generates a mutation when there is a difference. In the first two items React doesn't see differences but in the third there is a new one, so it adds it. Until now everything is efficient but now let's add the name at the beginning.

```react
  <ul>
    <li>Ben</li>
    <li>Bob</li>
  <ul>
  // add name:
  <ul>
    <li>Juan</li>
    <li>Ben</li>
    <li>Bob</li>
  <ul>
```

Now React when iterating see differences everywhere so it ends generating every item again. You see the first place has a different item compared with the second list and this is repeated. This is not efficient.

In order to solve this, React supports the key attribute:

- List with key attribute

When children have keys React uses the key to match children in the original tree with the children in the subsequent tree. Now React knows the element with key "3" is the new one and "1" and "2" have just moved so React isn't gonna render them again, just "3" at the beginning.

```react
  <ul>
    <li key="1">Ben</li>
    <li key="2">Bob</li>
  <ul>
  // add name:
  <ul>
    <li key="3">Juan</li>
    <li key="1">Ben</li>
    <li key="2">Bob</li>
  <ul>
```

## Index as Key Anti-pattern

If we don't have an id as before, we could just use the name as key but the name could not be a unique value and we could get errors. An alternative is using indexes. The second parameter in the arrow function is the index of the element in the current iteration.

```react
const NameList = () => {
  const names = ["Ben", "Bob", "Juan"];

  const nameList = names.map((name, index) => (
    <h2 key={index}>
      {index} {"<"}-- {name}
    </h2>
  ));
  return <div>{nameList}</div>;
};

export default NameList;
```

![img-description](/indexes.png)

### What Could Go Wrong?

Using index as key could be problematic in some scenarios

```react
// App.js
import IndexProblems from "./components/IndexProblems";

function App() {
  return (
    <div className="App">
      <IndexProblems />
    </div>
  );
}

export default App;
```

```react
// ToDo.js

const ToDo = (props) => (
  <tr>
    <td>
      <label>{props.index}</label>
    </td>
    <td>
      <label>{props.id}</label>
    </td>
    <td>
      <input />
    </td>
    <td>
      <label>{props.createdAt.toLocaleTimeString()}</label>
    </td>
  </tr>
);

export default ToDo;
```

```react
// IndexProblems.js
import { Component } from "react";
import ToDo from "./Todo";

class IndexProblems extends Component {
  constructor() {
    super();
    const date = new Date();
    const todoCounter = 1;
    this.state = {
      todoCounter: todoCounter,
      list: [
        {
          id: todoCounter,
          createdAt: date,
        },
      ],
    };
  }

  sortByEarliest = () => {
    const sortedList = this.state.list.sort((a, b) => {
      return a.createdAt - b.createdAt;
    });
    this.setState({
      list: [...sortedList],
    });
  };

  sortByLatest = () => {
    const sortedList = this.state.list.sort((a, b) => {
      return b.createdAt - a.createdAt;
    });
    this.setState({
      list: [...sortedList],
    });
  };

  addToEnd = () => {
    const date = new Date();
    const nextId = this.state.todoCounter + 1;
    const newList = [...this.state.list, { id: nextId, createdAt: date }];
    this.setState({
      list: newList,
      todoCounter: nextId,
    });
  };

  addToStart = () => {
    const date = new Date();
    const nextId = this.state.todoCounter + 1;
    const newList = [{ id: nextId, createdAt: date }, ...this.state.list];
    this.setState({
      list: newList,
      todoCounter: nextId,
    });
  };

  render() {
    return (
      <div>
        <code>key=index</code>
        <br />
        <button onClick={this.addToStart}>Add New to Start</button>
        <button onClick={this.addToEnd}>Add New to End</button>
        <button onClick={this.sortByEarliest}>Sort by Earliest</button>
        <button onClick={this.sortByLatest}>Sort by Latest</button>
        <table>
          <tr>
            <th>Index</th>
            <th>ID</th>
            <th>Item</th>
            <th>Created at</th>
          </tr>
          {this.state.list.map((todo, index) => (
            <ToDo key={index} index={index} {...todo} />
          ))}
        </table>
      </div>
    );
  }
}

export default IndexProblems;

```

![description](/todoApp.png)

"Index" specify the position of the item in the list, "ID" is self incremented, in "Item" there is a input where we can enter something and "Created at" timestamp

![description](/todoApp2.png)

If I add 3 items with "Add New to End" everything looks good.

![description](/todoApp3.png)

But when I add a new item with "Add New to Start" something unexpected happens. Index, ID and timestamp are right but the item value is not blank as expected.

That's because React is comparing every key as I commented before.

```react
  <ul>
    <li key="0">Ben</li>
    <li key="1">Bob</li>
  <ul>
  // When we try to add a value to the beginning, that value is gonna have an index of 0
  // but React compare keys with index 0 and 1 and see that we already have values there
  <ul>
    <li key="0">Ben</li>
    <li key="1">Bob</li>
    <li key="2"></li>
  <ul>
```

We have the same problem when we try to sort the list.

### When to use index as a key?

- The items in your list do not have a unique id
- The list is STATIC and WON'T CHANGE
- The list will NEVER be REORDERED or FILTERED

If there is no id you could use npm packages that generate unique IDs for a list or try hashing out a unique value from one of the existing properties.

React uses index as key by default.

TRY TO AVOID USING INDEX AS KEY.

## Styling and CSS Basics

### CSS Stylesheets

```react
// App.js
import Stylesheet from "./components/Stylesheet";

function App() {
  return (
    <div className="App">
      <Stylesheet primary={false}/>
    </div>
  );
}

export default App;
```

```css
.primary {
    color: yellow
}

.font-xl {
    font-size: 5rem;
}
```

```react
// Stylesheet.js
import "./myStyles.css";

const Stylesheet = ({ primary }) => {
  let className = primary ? "primary" : "";

  return (
    <div>
      <h1 className={`${className} font-xl`}>Styles</h1>
    </div>
  );
};

export default Stylesheet;
```

### Inline Styling

```react
const Inline = () => {
  const heading = {
    color: "red",
    fontSize: "5rem", // NOTICE camelCase (fontSize, not font-size)
  };

  return (
    <div>
      <h1 style={heading}>Inline</h1>
    </div>
  );
};

export default Inline;
```

### CSS Modules

Without modules styles can be used by children. With modules not.

So styles from "import styles from "./AppStyles.module.css";" can only be used where invoked. If you don't use this approach other components could use styles from this css file. Examples below.

```css
/* appStyles.css */
.error {
  color: red;
}
```

```css
/* appStyles.component.css */
/* Notice the file name uses "component" */
.success {
  color: green;
}
```

```react
// App.js
import "./appStyles.css";
import styles from "./appStyles.module.css";
import Welcome from "./components/Welcome";


function App() {
  return (
    <div className="App">
      <h1 className="error">ERROR</h1>
      // error can be used here and by children
      <h1 className={styles.success}>SUCCESS</h1>
      // success can be used only in App.js
    </div>
    <Welcome />
  );
}

export default App;
```

SO THIS WORKS:

```react
// Welcome.js
const Welcome = () => {
  return <h1 className="error">Hello, World!</h1>;
};

export default Welcome;
```

BUT THIS NOT:

```react
// Welcome.js
const Welcome = () => {
  return <h1 className={styles.success}>Hello, World!</h1>;
};

export default Welcome;
```

This could be useful to prevent accidents with styles.

### CSS in JS Libraries

Out of scope

## Basics of Form Handling

In regular HTML form elements like input, text area an so on are responsible by their own to handle the user input and update the respective values. Now we wanna React to control form elements instead, we wanna create controlled components.

### Class Component Version

```react
import { Component } from "react";

class Form extends Component {
  constructor(props) {
    super(props);

    this.state = {
      username: "",
      comments: "",
      topic: "angular",
    };
  }

  handleUsernameChange = (event) => {
    this.setState({
      username: event.target.value,
    });
  };

  handleCommentsChange = (event) => {
    this.setState({
      comments: event.target.value,
    });
  };

  handleTopicChange = (event) => {
    this.setState({
      topic: event.target.value,
    });
  };

  handleSubmit = (event) => {
    alert(
      `
      Username: ${this.state.username}
      Comments: ${this.state.comments}
      Topic: ${this.state.topic}
      `
    );

    // CLEAN STATE:
    this.setState({
      username: "",
      comments: "",
      topic: "",
    });

    // AVOID REFRESHING THE PAGE:
    event.preventDefault();
  };

  render() {
    const { username, comments, topic } = this.state;

    return (
      <form onSubmit={this.handleSubmit}>

        <div>
          <label>Username</label>
          <input
            type="text"
            value={username}
            onChange={this.handleUsernameChange}
          />
        </div>

        <div>
          <label>Comments</label>
          <textarea
            value={comments}
            onChange={this.handleCommentsChange}
          ></textarea>
        </div>

        <div>
          <label>Topic</label>
          <select value={topic} onChange={this.handleTopicChange}>
            <option value="react">React</option>
            <option value="angular">Angular</option>
            <option value="vue">Vue</option>
          </select>
        </div>

        <button type="submit">SUBMIT</button>
      </form>
    );
  }
}

export default Form;
```

### Function Component Version

```react
import { useState } from "react";

const FormFunctionVersion = () => {
  const [username, setUsername] = useState("");
  const [comments, setComments] = useState("");
  const [topic, setTopic] = useState("angular");

  const handleUsernameChange = (event) => {
    setUsername(event.target.value);
  };

  const handleCommentsChange = (event) => {
    setComments(event.target.value);
  };

  const handleTopicChange = (event) => {
    setTopic(event.target.value);
  };

  const handleSubmit = (event) => {
    alert(
      `
      Username: ${username}
      Comments: ${comments}
      Topic: ${topic}
      `
    );

    // CLEAN STATE:
    setUsername("");
    setComments("");
    setTopic("");

    // AVOID REFRESHING THE PAGE:
    event.preventDefault();
  };

  return (
    <form onSubmit={handleSubmit}>

      <div>
        <label>Username</label>
        <input type="text" value={username} onChange={handleUsernameChange} />
      </div>

      <div>
        <label>Comments</label>
        <textarea value={comments} onChange={handleCommentsChange}></textarea>
      </div>

      <div>
        <label>Topic</label>
        <select value={topic} onChange={handleTopicChange}>
          <option value="react">React</option>
          <option value="angular">Angular</option>
          <option value="vue">Vue</option>
        </select>
      </div>

      <button type="submit">SUBMIT</button>

    </form>
  );
};

export default FormFunctionVersion;
```

## Lifecycle Methods (Class Components)

When we create a react component, the component goes through several stages in its lifecycle. React provides us with built in methods that we can override at particular stages in the lifecycle. Let's take a look at the lifecycle methods available for a class component. These method don't exist in functional components, there we use useEffect hooks.

We can mainly clasify the methods into four phases:

### Mounting

When an instance of a component is being created and inserted into the DOM.

Main methods:

1 - constructor(props)

* Special function that will get called whenever a new component is created.
* Used for initialiazing state and binding the event handlers.
* DON'T cause side effects here. Ex: HTTP request.
* You have to call a special function: super(props). We have access to this.props only after using it.
* From here we handle the state.

2 - static getDerivedStateFromProps(props, state)

* Rarely used.
* Used when the state of the component depends on changes in props overtime. So let's say you have a component, where the initial state of the component depends on the props being passed to the component. In such scenario you can use this method to set the state. Since this method is a static method it doesn't have access to the "this" keyword so you cannot call "this.state" within this method. Instead you have to return an object that represent the new state of the component.
* You shouldn't cause side effects. Ex: fetching data from an endpoint.

3 - render()

* It's the only required method in Class Components.
* We read "this.props", "this.state" and return JSX wich describes the UI.
* It's a pure function. For the given props and state it should always render the same UI.
* You shouldn't change the state of the component, interacting with the DOM or making any AJAX call here.
* Children components lifecycle methods are also executed.

4 - componentDidMount()

* Called only once in the whole lifecycle of a given component.
* Invoked immediately after a component and all its children components have been rendered to the DOM.
* It's the perfect place to cause side effects. Ex: interact with the DOM or perform any AJAX calls to load data.

Let's see the order of execution in code:

```react
// LifecycleA.js
import { Component } from "react";

class LifecycleA extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "Ben",
    };
    console.log("LifecycleA constructor");
  }

  static getDerivedStateFromProps(props, state) {
    console.log("LifecycleA getDerivedStateFromProps");
    return null;
  }

  componentDidMount() {
    console.log("LifecycleA componentDidMount");
  }

  render() {
    console.log("LifecycleA render");
    return <div>LifecycleA</div>;
  }
}

export default LifecycleA;
```

For some reason everything is logged twice:

![description](/lifecycleMethodsConsole.png)

If we now create almost the same file but now with "LifecycleB" and we import it in "LifecycleA" in order to render it as a child component, we will see in the console something like this:

```shell
LifecycleA constructor
LifecycleA getDerivedStateFromProps
LifecycleA render

LifecycleB constructor
LifecycleB getDerivedStateFromProps
LifecycleB render
LifecycleB componentDidMount

LifecycleA componentDidMount
```

```react
// LifecycleA.js
import { Component } from "react";
import LifecycleB from "./LifecycleB";

class LifecycleA extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "Ben",
    };
    console.log("LifecycleA constructor");
  }

  static getDerivedStateFromProps(props, state) {
    console.log("LifecycleA getDerivedStateFromProps");
    return null;
  }

  componentDidMount() {
    console.log("LifecycleA componentDidMount");
  }

  render() {
    console.log("LifecycleA render");
    return (
      <div>
        <div>LifecycleA</div>
        <LifecycleB />
      </div>
    );
  }
}

export default LifecycleA;
```

```react
// LifecycleB.js
import { Component } from "react";

class LifecycleB extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "Ben",
    };
    console.log("LifecycleB constructor");
  }

  static getDerivedStateFromProps(props, state) {
    console.log("LifecycleB getDerivedStateFromProps");
    return null;
  }

  componentDidMount() {
    console.log("LifecycleB componentDidMount");
  }

  render() {
    console.log("LifecycleB render");
    return <div>LifecycleB</div>;
  }
}

export default LifecycleB;
```

### Updating

When a component is being re-rendered as a result of changes to either its props or state.

Main methods:

1. static getDerivedStateFromProps(props, state)
* We just saw this method in Mounting phase.
* Return null or an object representing the updated state.
* Called everytime the component is re-rendered.
* You shouldn't cause any side-effect here. Ex: HTTP request.
* It is not a common used method.
2. shouldComponentUpdate(nextProps, nextState)
* Receives the updated props and state.
* Dictates if the component should re-render or not. By default components will re-render whenever props or state changes. This method can prevent that default behavior by returning false. You can compare existing props and state values with the next ones and return true or false to let React know whether the component should update or not.
* It is for performance optimization.
3. render()
* I already talk about it in Mounting phase.
4. getSnapshotBeforeUpdate(prevProps, prevState)
* Called right before the changes from the virtual DOM are to be reflected in the DOM.
* Capture some information from the DOM. Ex: read users scroll position and after update maintain that scroll position by performing some calculations.
* Will either return null or return a value. Returned value will be passed as the third parameter to the next method.
* Not common method.
5. componentDidUpdate(prevProps, prevState, snapshot)
* Called after the render is finished in the re-render cycles. You can be sure that the component and all its sub components have properly rendered itself after the update.
* Is guaranteed to be called only once in each re-render cycle.
* You can cause side-effects here. But before making the call you need to compare the previous props with the new props and then decide whether to make the call or not. Otherwise you are making unwanted requests which is not good.

Let's see the order of execution in code. The button in "LifecycleA.js" is gonna change the state:

```bash
LifecycleA getDerivedStateFromProps
LifecycleA shouldComponentUpdate
LifecycleA render

LifecycleB getDerivedStateFromProps
LifecycleB shouldComponentUpdate
LifecycleB render
LifecycleB getSnapshotBeforeUpdate

LifecycleA getSnapshotBeforeUpdate

LifecycleB componentDidUpdate

LifecycleA componentDidUpdate
```

```react
// LifecycleA.js
import { Component } from "react";
import LifecycleB from "./LifecycleB";

class LifecycleA extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "Ben",
    };
    console.log("LifecycleA constructor");
  }

  static getDerivedStateFromProps(props, state) {
    console.log("LifecycleA getDerivedStateFromProps");
    return null;
  }

  shouldComponentUpdate() {
    console.log("LifecycleA shouldComponentUpdate");
    return true;
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log("LifecycleA getSnapshotBeforeUpdate");
    return null;
  }

  componentDidUpdate() {
    console.log("LifecycleA componentDidUpdate");
  }

  changeState = () => {
    this.setState({
      name: "Homer",
    });
  };

  render() {
    console.log("LifecycleA render");
    return (
      <div>
        <div>LifecycleA</div>
        <button onClick={this.changeState}>CHANGE STATE</button>
        <LifecycleB />
      </div>
    );
  }
}

export default LifecycleA;
```

```react
// LifecycleB.js
import { Component } from "react";

class LifecycleB extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "Ben",
    };
    console.log("LifecycleB constructor");
  }

  static getDerivedStateFromProps(props, state) {
    console.log("LifecycleB getDerivedStateFromProps");
    return null;
  }

  shouldComponentUpdate() {
    console.log("LifecycleB shouldComponentUpdate");
    return true;
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log("LifecycleB getSnapshotBeforeUpdate");
    return null;
  }

  componentDidUpdate() {
    console.log("LifecycleB componentDidUpdate");
  }

  render() {
    console.log("LifecycleB render");
    return <div>LifecycleB</div>;
  }
}

export default LifecycleB;
```

### Unmounting

When component is being removed from the DOM.

Main method:

1. componentWillUnmount()
* Invoked immediately before a component is unmounted and destroyed.
* Used for cancelling any network request, removing event handlers, cancelling any subscription and also invalidating timers.
* Don't call the setState method, the component is never re-rendered after being unmounted.

### Error Handling

When there is an error during rendering, in a lifecycle method, or in the constructor of any child component.

Main methods:

1. static getDerivedStateFromError(error)
2. componentdidCatch(error, info)

## Fragments

Let you group a list of children elements without adding extra nodes to the DOM. They are the empty <> and </>.

Without Fragments we are adding an aditional element (div) in order to wrap with the outer element:

```react
// FragmentDemo.js
const FragmentDemo = () => {
  return (
    <div>
      <h1>Fragment Demo</h1>
      <p>Description</p>
    </div>
  );
};

export default FragmentDemo;
```

With Fragment we don't add extra nodes to the DOM:

```react
// FragmentDemo.js
const FragmentDemo = () => {
  return (
    <>
      <h1>Fragment Demo</h1>
      <p>Description</p>
    </>
  );
};

export default FragmentDemo;
```

There are scenarios where it is very helpful. Ex: when working with tables:

```react
// Table.js
import Columns from "./Columns";

const Table = () => {
  return (
    <table>
      <tbody>
        <tr>
          <Columns />
        </tr>
      </tbody>
    </table>
  );
};

export default Table;
```

```react
// Columns.js
const Columns = () => {
  return (
    <div>
      <td>Name</td>
      <td>Ben</td>
    </div>
  );
};

export default Columns;
```

We are gonna see warnings in the console because we are wrapping the columns into a div. "<td> cannot appear as a child of <div>". Leave the divs empty (use Fragments) to fix it.

```react
// Columns.js
const Columns = () => {
  return (
    <>
      <td>Name</td>
      <td>Ben</td>
    </>
  );
};

export default Columns;
```
<> and </> is the short syntax, you could see < React.Fragment> and < /React.Fragment> as well.

### Keyed Fragments

Fragments declared with the explicit < React.Fragment> syntax may have keys. A use case for this is mapping a collection to an array of fragments — for example, to create a description list:

```react
// Glossary.js
import React from "react";

const Glossary = (props) => {
  return (
    <dl>
      {props.items.map((item) => (
        // Without the `key`, React will fire a key warning
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
};

export default Glossary;
```
## Pure Components

We have been creating class components by extending the component class from React. There is a second way to create a class component, by extending the pure component class from React.

Pure Components by preventing unnecessary renders can give performance boost in certain scenarios.

You shouldn't mutate object or arrays in props or state. Ex: if you need to add a new item to a list, don't mutate it by pushing the item into the list. The reference never changes and because Pure Components only check for that, the component won't re-render even if there is a difference. Always return a new object o array when dealing with Pure Components.

### Regular Components VS Pure Components
If you change the component state and it is the same as before, with Pure Components the component is not gonna be re-rendered but with Regular Components it is gonna be render the same state.

In the following examples we are changing the state to the same name every two seconds. If Pure, the component is rendered once, if not Pure, the component is rendered every two seconds even if it has the same value.

You'll see in the console:

```bash
********************Parent Component render********************
Regular Component render
Pure Component render
********************Parent Component render********************
Regular Component render
********************Parent Component render********************
Regular Component render
********************Parent Component render********************
Regular Component render

And so on every 2 seconds...
```

```react
// ParentComp.js
import { Component } from "react";
import RegularComp from "./RegularComp";
import PureComp from "./PureComp";

class ParentComp extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "Ben",
    };
  }

  componentDidMount() {
    setInterval(() => {
      this.setState({
        name: "Ben",
      });
    }, 2000);
  }

  render() {
    console.log(
      "********************Parent Component render********************"
    );
    return (
      <div>
        Parent Component
        <RegularComp name={this.state.name} />
        <PureComp name={this.state.name} />
      </div>
    );
  }
}

export default ParentComp;
```

```react
// PureComp.js
import { PureComponent } from "react";

class PureComp extends PureComponent {
  render() {
    console.log("Pure Component render");

    return <div>Pure Component {this.props.name}</div>;
  }
}

export default PureComp;
```

```react
// RegularComp.js
import { Component } from "react";

class RegularComp extends Component {
  render() {
    console.log("Regular Component render");

    return <div>Regular Component {this.props.name}</div>;
  }
}

export default RegularComp;
```

* A Regular Component doesn't implement the shouldComponentUpdate method. It always returns true by default.
* A Pure Component on the other hand implements shouldComponentUpdate with a shallow props and state comparison.

## Shallow Comparison (SC)

* Primitive Types

a (SC) b returns true if a and b have the same value and are of the same type.

Ex: string "Ben" (SC) string "Ben" returns true.

* Complex Types

a (SC) b return true if a and b reference the exact same object. Ex:

```react
let a = [1, 2, 3]
let b = [1, 2, 3]
let c = a

let ab_eq = (a === b) //false
let ac_eq = (a === c) //true

let a = { x: 1, y: 2}
let b = { x: 1, y: 2}
let c = a

let ab_eq = (a === b) //false
let ac_eq = (a === c) //true
```

### Summary

* A pure component implements shouldComponentUpdate with a shallow props and state comparison.

1. SC of prevState with currentState

2. SC of prevProps with currentProps

3. If difference, re-render component.

* Ensure that all the children components are also pure to avoid unexpected behaviour.

* Never mutate the state. Always return a new object that reflects the new state.

* It's probably safe to use Regular Components most of the time unless you see a performance head in some components.

## Refs

Allow access DOM nodes directly within React. Let's see the most common case: focus a text input. Ex: as soon as the page loaded by default we want the username input field to be focused.

* Steps:

1. Create a ref with React.createRef(). It's common to create refs in the constructor so that they can be referenced throughout the component.

2. Attach this ref to our input element in the render method with the reserved ref attribute.

3. Call the focus method on this input element.

With the console.log() we can see an object logged in the console. If expanded, there is a property called current of type input and this current property points to the actual DOM node. To focus the input element in componentDidMount() we call the focus method on the current property.

```react
// RefsDemo.js
import React, { Component } from "react";

class RefsDemo extends Component {
  constructor(props) {
    super(props);
    this.inputRef = React.createRef(); //step 1
  }

  componentDidMount() {
    this.inputRef.current.focus(); //step 3
    console.log(this.inputRef);
  }

  render() {
    return (
      <div>
        <input type="text" ref={this.inputRef} /> {/*step 2*/}
      </div>
    );
  }
}

export default RefsDemo;
```

Another possible use case for Refs could be to fetch the input value.

```react
import React, { Component } from "react";

class RefsDemo extends Component {
  constructor(props) {
    super(props);
    this.inputRef = React.createRef();
  }

  clickHandler = () => {
    alert(this.inputRef.current.value);
  };

  render() {
    return (
      <div>
        <input type="text" ref={this.inputRef} />
        <button onClick={this.clickHandler}>CLICK</button>
      </div>
    );
  }
}

export default RefsDemo;
```
### Callback Ref

This is a second way to set refs. It is an old approach.

* Steps:

1. Create the ref by creating a property and assigning a value of null.

2. Create a method that will assign a DOM element to the ref we have just created in step 1.

3. Attach the ref to the input element.

React will call the ref callback with the DOM element when the component mounts and call it with null when it unmount, that's the reason it's important to check if a value exists on the reference property and that it isn't null.

Unlike the previous approach the DOM node is directly accessed using the ref property, we don't need "current".

```react
import React, { Component } from "react";

class RefsDemo extends Component {
  constructor(props) {
    super(props);

    this.callbackRef = null; //step 1

    //step 2:
    this.setCallbackRef = (element) => {
      this.callbackRef = element;
    };
  }

  componentDidMount() {
    if (this.callbackRef) {
      this.callbackRef.focus();
    }
  }

  clickHandler = () => {
    if (this.callbackRef) {
      alert(this.callbackRef.value);
    }
  };

  render() {
    return (
      <div>
        <input type="text" ref={this.setCallbackRef} /> {/*step 3*/}
        <button onClick={this.clickHandler}>CLICK</button>
      </div>
    );
  }
}

export default RefsDemo;
```

## Refs with Class Components

We saw how to add refs to a normal HTML element like the input element. It's also possible to add a ref to a class component. Let's attach refs to DOM elements and also class components.

When clicking parent component button, child component input is focused.

```react
// App.js
import FocusInput from "./components/FocusInput";

function App() {
  return (
    <div className="App">
      <FocusInput />
    </div>
  );
}

export default App;
```

```react
// Input.js
import React, { Component } from "react";

class Input extends Component {
  constructor(props) {
    super(props);
    this.inputRef = React.createRef();
  }

  focusInput = () => {
    this.inputRef.current.focus();
  };

  render() {
    return (
      <div>
        <input type="text" ref={this.inputRef} />
      </div>
    );
  }
}

export default Input;
```

```react
// FocusInput.js
import React, { Component } from "react";
import Input from "./Input";

class FocusInput extends Component {
  constructor(props) {
    super(props);

    this.componentRef = React.createRef();
  }

  clickHandler = () => {
    this.componentRef.current.focusInput();
  };

  render() {
    return (
      <div>
        <Input ref={this.componentRef} />
        <button onClick={this.clickHandler}>Focus Input</button>
      </div>
    );
  }
}

export default FocusInput;
```
So if you need a ref to a child component from a parent component, that's possible. And the component however has to be a class component, refs cannot be attached to functional components.

## Forwarding Refs

It's a technique for automatically passing a ref through a component to one of its children
Unlike in the previous "Refs with Class Components" where the ref was pointing to the class component, here we'll use the forwarding ref technique to allow the parent component to directly reference the native input element.

* Steps:

1. Create a ref in the parent component.

2. Attach the ref to the child component using the ref attribute.

3. Forward this ref to the input element in the child component and ref forwarding can be achieved using the forward draft method from the React Library.

```react
// App.js
import ForwardRefParentInput from "./components/ForwardRefParentInput";

function App() {
  return (
    <div className="App">
      <ForwardRefParentInput />
    </div>
  );
}

export default App;
```

```react
// ForwardRefInput.js
import React from "react";

// step 3:
const ForwardRefInput = React.forwardRef((props, ref) => {
  return (
    <div>
      <input type="text" ref={ref} />
    </div>
  );
});

export default ForwardRefInput;
```

```react
// ForwardRefParentInput.js
import React, { Component } from "react";
import ForwardRefInput from "./ForwardRefInput";

class ForwardRefParentInput extends Component {
  constructor(props) {
    super(props);

    this.inputRef = React.createRef(); //step 1
  }

  clickHandler = () => {
    this.inputRef.current.focus();
  };

  render() {
    return (
      <div>
        <ForwardRefInput ref={this.inputRef} /> {/*step 2*/}
        <button onClick={this.clickHandler}>FOCUS INPUT</button>
      </div>
    );
  }
}

export default ForwardRefParentInput;
```

## Portals

ReactDOM.createPortal(child, container)

Provides a way to render children into a DOM node that exists outside the DOM hierarchy of the parent component.
So far we have had one DOM element in our HTML that we were mounting the React App into, if you go into the public folder and "index.html" you can see the element with ID equal to root. In our "index.js" file we use ReactDOM.render and mount our app component onto the root element so if we take a look at the browser in the DOM tree every single React component in our app falls under the root element (div element with ID equal to root).

React Portals provide the ability to break out of this DOM tree so you can render a component onto a DOM node that is not under this root element.

* Steps:

1. Add a DOM node that falls outside the root element. So in "index.html" right below the root element I'm gonna add another div tag with ID equal to "portal-root".

2. Create a new component.

3. Use reactDOM.createPortal() to insert this component under the "portal-root" node.

```html
<!--index.html-->
<!DOCTYPE html>
<html lang="en">
  <head>
    ...
  </head>
  <body>
    <div id="root"></div>
    <div id="portal-root"></div>  <!--step 1-->
  </body>
</html>
```

```react
// PortalDemo.js
import ReactDOM from "react-dom";

//step 2 and 3
const PortalDemo = () => {
  return ReactDOM.createPortal(
    <h1>PortalDemo</h1>,
    document.getElementById("portal-root")
  );
};

export default PortalDemo;
```

Go to Dev Tools and see where is being rendered now the component.

You can render any element that React can render (passing it in the "ReactDOM.createPortal" as first parameter) in any DOM element you select (second parameter).

### Why Portals?

A typical use case for portals is when a parent component has an overflow: hidden or z-index style, but you need the child to visually “break out” of its container. For example, dialogs, hovercards, and tooltips.

## Error Boundary

It's a class component that implements either one or both of the lifecycle methods, getDerivedStateFromError or componentDidCatch becomes an error boundary.

The static method getDerivedStateFromError method is used to render a fallback UI after an error is throw and the componentDidCatch method is used to log the error information.

We saw that runtime errors durin rendering could put our app in a broken state, React basically unmounts the whole React component tree. What would be great is if we could catch the errors anywhere in ther component tree and display a fallback UI.

```react
// App.js
import Hero from "./components/Hero";
import ErrorBoundary from "./components/ErrorBoundary";

function App() {
  return (
    <div className="App">
      // ErrorBoundary is wrapping every child component
      // because otherwise when an error happens the fall-back UI would
      // cover the entire app.
      <ErrorBoundary>
        <Hero heroName={"Iron Man"} />
      </ErrorBoundary>
      <ErrorBoundary>
        <Hero heroName={"Kick Ass"} />
      </ErrorBoundary>
      <ErrorBoundary>
        <Hero heroName={"Joker"} />
      </ErrorBoundary>
    </div>
  );
}

export default App;
```

```react
//Hero.js
import React from "react";

const Hero = ({ heroName }) => {
  if (heroName === "Joker") {
    throw new Error("Not a hero!");
  }

  return <h1>{heroName}</h1>;
};

export default Hero;
```

```react
//ErrorBoundary.js
import React, { Component } from "react";

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);

    this.state = {
      hasError: false,
    };
  }

  static getDerivedStateFromError(error) {
    return {
      hasError: true,
    };
  }

  componentDidCatch(error, info) {
    console.log(error);
    console.log(info);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong</h1>;
    }
    return this.props.children;
  }
}

export default ErrorBoundary;
```
Error boundaries catch errors during rendering in lifecycle methods and constructors of the whole tree below them. However they don't catch errors inside event handlers. If you have an onClick handler and wanna catch an error you just need to use the regular try/catch statements and not error boundaries.

* Error boundaries are React components that catch JavaScript error in their child component tree, log those errors and display a fall-back UI.

* A class component becomes an Error Boundary by defining either or both of getDerivedStateFromError and componentDidCatch lifecycle methods.

In our example in the lifecycle method we set the hasError state to true which causes the fallback UI to be rendered.

* The placement of the Error Boundary also matters as it controls if the entire app should have the fall-back UI or just the component causing the problem.

* Provide a way to gracefully handle error in application code.




