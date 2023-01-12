---
title: React Render Tutorial
date: 2023-01-08
categories: [React]
tags: [render] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

When you run React apps the code written in our components get translated into elements that get mounted onto the DOM. The React documentation splits this work into two phases: Render Phase and Commit Phase.

## Initial Render

### Render Phase

During this phase React will start at the root of the component tree and go downwards to the leaf components. While traversing for each of the components React invokes the createElement method and converts the component JSX into React elements and stores that render output. React elements are basically JavaScript objects that describe the structure of you UI. Once the JSX to React elements conversion is done for the entire component tree all the React elements are handed over to the Commit Phase.

### Commit Phase

The React elements are applied to the DOM using the ReactDOM package.

![Render and commit phases in initial render](/render-and-commit-phases-in-initial-render.png)

However this is the rendering behavior for just the initial render of your React app and React components need to be re-render in order to update the UI. So let's also understand the two phases but this time with respect to a re-render scenario.

## Re-render

### Render Phase

During the Render Phase React will start at the root of the component tree and goes downwards to the leaf components finding all the components that has been flagged as needing updates. A component can flag itself for an update by calling the useState setter function or calling the useReducer dispatch function. For each of the flagged components React will invoke the createElement method and converts the components JSX into React elements and stores that render output. Once the convertion is done for all the flagged components and the components affected by the flag component React will compare the set of React elements with the ones that were produced from the last render. A list is created with all the changes that need to be made to the DOM and handed over to the Commit Phase.

### Commit Phase

During the Commit Phase the changes are actually applied to the DOM. What I wanna stress here is the fact that rendering is not the same as updating the DOM. This distinction is very important because a component may be rendered without any visible changes to the DOM, for example, during rendering the component converts into the same React element as it did in the previous render, the elements are discarded and no changes are applied to the DOM. This distinction is even more necessary because a common notion is that the performance issues are because of slow DOM updates, actually React handles DOM updates efficiently in the sense that all updates are batched and updated at once. This helps reduce the performance issues incurred by updating the DOM multiple times in rapid succession. In fact if you refer to the React Docs it states that "the Commit Phase is usually very fast, but rendering can be slow".

This knowledge will help you write more performant React apps.

- Render Phase

1. Find all elements flagged for update.
2. For each flagged component, convert JSX to React element and store the result.
3. Perform reconciliation - Diff old and new tree of React elements (a.k.a Virtual DOM).
4. Hand over the changes to the next phase.

- Commit Phase

1. Apply changes to the DOM.

![Render and commit phases in re-renders](/render-and-commit-phases-in-re-render.png)

## useState

```react
import MagicalState from "./components/UseState/MagicalState";

function App() {
  return (
    <div className="App">
      <MagicalState />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";

const MagicalState = () => {
  const [count, setCount] = useState(0);

  console.log("useState Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
    </div>
  );
};

export default MagicalState;
```

On page load if we take a look at the console we can see two log statements. If we check our component though we just have one log statement, but the statement was logged twice. This can be really confusing at first but this is because of the Strict Mode encouraged by Create React App. If you take a look at index.js you can see that our app component is wrapped with "React.StrictMode". What this wrapper does is intentionally double invoke the function component body only in development mode. If you deploy to production however you'd see the log statement only once as intended. Now if you wanna understand why the Strict Mode double invokes or just wanna learn more about Strict Mode in general I'd advise you to go through the React Docs. For now the fix is to simply comment out this wrapper component. Now the log statement should appears only once in the browser.

After the initial render one of the ways to flag a component for re-render is by calling the setter function from useState. In our component we're calling setCount on click of the button. If you go back to the browser, clear the console and click on the button you can see that useState render is logged in the console. With every subsequent click the setCount function will flag or queue a re-render of our component and the message is logged in the console everytime.

Let's understand this re-rendering behavior with respect to the Render and Commit Phases:

1. We begin with the component tree. We have the App component and the MagicalState component. When we click on the button in the MagicalState component the state hook setter function is called which flags the MagicalState component as needing an update. During the Render Phase React will first go through the component tree and identify the flagged components. It says that MagicalState is the only component that needs an update.

2. React then uses the createElement method to convert the component JSX into a react element

3. It'll then diff the element produced from the previous render to the new render, it'll identify changes and hand them over to the Commit Phase where the changes are applied to the DOM.

![Render and commit phases in re-renders](/when-re-render.png)

This is what happens when you use the state hook in a react component. Now what is a special case with useState and re-rendering is that if you update a state hook to the same value as the current state React may render that component one more time and then bail out from subsequent renders.

Example:

```react
import { useState } from "react";

const MagicalState = () => {
  const [count, setCount] = useState(0);

  console.log("useState Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setCount(0)}>Count to 0</button>
      <button onClick={() => setCount(5)}>Count to 5</button>
    </div>
  );
};

export default MagicalState;
```

After the initial render if you call a setter function but set the state to the same value the component won't re-render. Try to reload the page and click on "Count to 0", no re-render happens. Now click on "Count - 0" button five times so the number becomes 5. The message has been logged five times. Now clear the console and click on "Count to 5" button. You can see the component render one more time, however if you click again the component doesn't re-render.

INITIAL RENDER --> SET SAME STATE VARIABLE VALUE --> NO RENDER

RE-RENDER --> SET SAME STATE VARIABLE VALUE --> ONE EXTRA RENDER

So after the component has been re-render if you set the state variable to the same value the component will re-render but only one more time.

- The setter function from a useState hook will cause the component to re-render.

- The exception is when you update a State Hook to the same value as the current state.

- Same value after the initial render? The component will not re-render.

- Same value after re-renders? React will render that specific component one more time and then bails out from any subsequent renders.

If you're wondering how the comparison is made between the previous and current state, React uses the object.is comparison algorithm.

## useReducer

useReducer behaves very similar to useState hook. The difference is that with useState we have the setter function but with useReducer we have the dispatch function.

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

const UseReducerDemo = () => {

  const [count, dispatch] = useReducer(reducer, initialState);

  console.log("UseReducerDemo Renders");

  return (
    <div>
      <h1>Current count: {count}</h1>
      <button onClick={() => dispatch("increment")}>INCREMENT</button>
      <button onClick={() => dispatch("decrement")}>DECREMENT</button>
      <button onClick={() => dispatch("reset")}>RESET</button>
    </div>
  );
};

export default UseReducerDemo;
```

You can experiment with the log statement as we did with useState.

## State Immutability

We've already seen that when it comes to useState and useReducer React doesn't re-render a component when the new state is the same as the old state. However our examples only involved working with primitive types.

Now let's see the rendering behavior when we try to mutate object and arrays.

```react
import { useState } from "react";

const initialState = {
  firstName: "Ben",
  lastName: "Z",
};

const ObjectUseState = () => {
  const [person, setPerson] = useState(initialState);

  const changeName = () => {
    person.firstName = "Juan";
    person.lastName = "Perón";
    setPerson(person);
  };

  console.log("ObjectUseState Render");

  return (
    <div>
      <button onClick={changeName}>
        {person.firstName} {person.lastName}
      </button>
    </div>
  );
};

export default ObjectUseState;
```

This is gonna fail. When we click on the button we're changing the properties of the person object and then calling the setter function. However you can see that the component doesn't re-render. This is because of the object.is algorithm that React uses. When we use object as state the reference to the object must change for the component to queue a re-render after comparing the old and the new state. This is a common mistake we tend to make as beginners, directly mutating the object and expecting the component to re-render. To fix this create a copy of the old state with the spread operator and then modify this new object copy.

```react
const changeName = () => {
  const newPerson = { ...person };
  newPerson.firstName = "Juan";
  newPerson.lastName = "Perón";
  setPerson(newPerson);
};
```

- This is also the case when dealing with array as state.

The following code is gonna fail because when clicking the button the component doesn't re-render and fails in adding the extra names. This again is due to the fact that array reference didn't change for React to queue up a re-render. When you push elements into the same array the array values change but the array reference itself doesn't change so React doesn't re-render the component.

```react
import { useState } from "react";

const initialState = ["Ben", "Juan"];

const ArrayUseState = () => {
  const [persons, setPersons] = useState(initialState);

  const handleClick = () => {
    persons.push("Jack");
    persons.push("Bob");
    setPersons(persons);
  };

  return (
    <div>
      <button onClick={handleClick}>CLICK</button>
      {persons.map((person) => {
        return <div key={person}>{person}</div>;
      })}
    </div>
  );
};

export default ArrayUseState;
```

To fix this make a copy of the array, push items and then pass the new array into the setter function:

```react
const handleClick = () => {
  const newPersons = [...persons];
  newPersons.push("Jack");
  newPersons.push("Bob");
  setPersons(newPersons);
};
```

This rendering behavior holds good for useReducer hook as well.

## Parent and Child

Now we're gonna understand the rendering behavior in React when dealing with parent and child components

```react
import Parent from "./components/ParentChild/Parent";

function App() {
  return (
    <div className="App">
      <Parent />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import Child from "./Child";

const Parent = () => {
  const [count, setCount] = useState(0);

  console.log("Parent Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setCount(0)}>Count to 0</button>
      <button onClick={() => setCount(5)}>Count to 5</button>
      <Child />
    </div>
  );
};

export default Parent;
```

```react
const Child = () => {
  console.log("Child Render");
  return <div>Child component</div>;
};

export default Child;
```

In the browser, on page load, we have two log messages Parent render and Child Render. This is because the default rendering behavior in React. When a Parent component renders React will recursively render all of its child components. In our example, on page load, the Parent component will render which in turn will cause the Child component to render, hence we have the two log statements.

We've already seen a single component rendering behavior with the useState hook, now let's understand how that affects a Child components rendering.

- The first case is when the new state is different
  from the old state. In the browser if I click on the first button the count value increments and in the console you can see there are two logs statements, one from the Parent and one from the Child. This is because the default behavior we understood a minute ago. useState setter function causes the Parent component to re-render and if the Parent component re-renders the Child component will also re-render.

- The second case is when the new state is the same as the old state. This can be further categorized into:

1. We call the setup function passing in the same state value right after the initial render. Let's reload the page and click on the second button "Count to 0". It calls the setter function but sets the state to 0 which is the default value which means the new state is the same as the old state. If we click on the button again we still don't see any log statement. We've already learned in useState section that if the old state is the same as the new state React bails out of re-rendering the component. In out case React builds out of re-rendering the Parent component and if the Parent component didn't re-render there's no need for the Child component to re-render, hence no log statement in the console.

2. We call the setup function passing in the same state value right after the component has re-rendered one or more times. Back in the browser let's reload the page and click on the third button "Count to 5". Since the new state is different from the old state React will re-render the Parent component, Child component also. Now comes the interesting part. Clear the console and click on the third button one more time. The setter function is called with the value of 5 again which means that the new state is the same as the old state. We've already learned in the useState section that after a re-render if the old state is the same as the new state React will re-render that component one more time as a safety measure which means that our component will re-render one more time. You can see the log message "Parent Render" BUT we don't see the message "Child Render" anymore! So this is an exception case. When you call a setter function or a dispatch function with the same state value after the re-render React will render the Parent component just one more time but won't re-render the Child components. The Parent component has to be re-render one more time just to be sure if it's safe to bail out from future renders. This is an example with useState hook but the same holds good for useReducer as well.

When we were clicking the first button the Parent and Child components re-rendered despite the DOM represented by Child component is never updated. Then when we clicked the third button twice, the second time the Child component went through the Render Phase but not the Commit Phase. This is commonly referred to as an unnecessary render. React went through so much work in the Render Phase only to ignore or discard the render output and not apply it to the DOM. If your app had 10 components and the topmost component re-renders all the other 9 children components will also re-render by default. Rendering itself isn't a bad thing, it's how React knows whether it needs to actually make any changes to the DOM. However unnecessary renders do affect performance and we'll learn more about optimization in the coming sections.

### Summary

- When a parent component renders, React will recursively render all of its child components.
- New state same as old state after initial render? Both Parent and Child don't re-render.
- New state same as old state after re-renders? Parent re-renders one more time but Child never re-renders.

## Same Element Reference

When I click on the button and change the state in the following example both Parent and Child components re-render. The child component goes through the Render Phase but the render output is ignored since nothing has changed from the previous render. The child component didn't make it to the Commit Phase. So at the moment we have a unnecessary render for the child component. Let's see how to optimize this rendering behavior.

```react
import { useState } from "react";
import ChildOne from "./ChildOne";

const ParentOne = () => {
  const [count, setCount] = useState(0);

  console.log("ParentOne Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <ChildOne />
    </div>
  );
};

export default ParentOne;
```

```react
const ChildOne = () => {

  console.log("ChildOne Render");

  return <div>ChildOne component</div>;
};

export default ChildOne;
```

I'm gonna remove the ChildOne component along with its import statement. In App.js for the ParentOne component I'm gonna include the ChildOne component as children. Finally I destructure children from props in ParentOne and I include it to the JSX. This is gonna render the ChildOne component. Now in the browser, in the initial render both are rendered but then when clicking the button again only the ParentOne is gonna be rendered. We have optimized our code to render only the ParentOne component and not the ChildOne component when there's a state change in the ParentOne component. Now ChildOne component is more of a prop than a nested component.

```react
import ChildOne from "./components/Optimization/ChildOne";
import ParentOne from "./components/Optimization/ParentOne";

function App() {
  return (
    <div className="App">
      <ParentOne>
        <ChildOne />
      </ParentOne>
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";

const ParentOne = ({ children }) => {
  const [count, setCount] = useState(0);

  console.log("ParentOne Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      {children}
    </div>
  );
};

export default ParentOne;
```

### Why this happens?

- Causes for re-render:

1. A component can re-render if it calls a setter function or a dispatch function.
2. A component can render if its parent component rendered.

When we had the ChildOne directly nested in the parent component JSX, the child component re-rendered when the parent component re-rendered. This is the default behavior in React. To optimize it we moved it from being invoked in the parent component JSX to being passed as a prop, it could be any prop but we chose to use children.

In our component tree when we call the setter function in the parent component, the component gets flagged for a re-render. React sees that the children prop is part of the JSX but this children prop references the ChildOne component. A component can change its state but it can never change its props. Taking this
into consideration React will automatically provide us with the optimization. You can picture React going about the re-render in the following way:

React looks at optimized ParentOne component. React says that it has to convert the button and the children prop to a react element. Now React tells itself that this re-render was caused by a state change in optimized ParentOne component. Since this component has no means of directly changing the props is receiving I'm sure the children props couldn't have changed. So instead of going through the Render Phase for the ChildOne component I'll simply make use of the react element that was previously created. That's how React assuring that the children props has to be referencing the same element from the previous render will skip the Render Phase for the ChildOne component.

If the component was re-rendering because of a props change then the ChildOne component would also have to be re-rendered:

```react
import GrandParent from "./components/Optimization/GrandParent";

function App() {
  return (
    <div className="App">
      <GrandParent />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import ParentOne from "./ParentOne";
import ChildOne from "./ChildOne";

const GrandParent = () => {
  const [newCount, setNewCount] = useState(0);

  return (
    <div>
      <button onClick={() => setNewCount((nc) => nc + 1)}>
        GrandParent Count - {newCount}
      </button>
      <ParentOne newCount={newCount}>
        <ChildOne />
      </ParentOne>
    </div>
  );
};

export default GrandParent;
```

If we cause ParentOne component to re-render because of a state change from the same component, React won't re-render the ChildOne component as it is still the same element which couldn't have changed. So if I click on count only ParentOne gets re-render. However I clear the console and now I click on the GrandParent count button you can see that both the ParentOne and ChildOne components are re-rendered. GrandParent changes its state and causes a re-render. This in turn will cause the ParentOne component to re-render. React now knows that ParentOne component isn't re-rendering because of its own state change but rather because of the parent component re-rendering, that means the props could have changed. So React will proceed with the Render Phase for the ChildOne component but of course ignores the render output and not committed to the DOM.

- In React, when a parent component renders, React will recursively render all of its child components.
- However, this can lead to unnecessary renders where the child component goes to their Render Phase but not the Commit Phase. So if you're experiencing performance issues you can extract the expensive child component and instead pass it down as props to the parent component.
- Whenever there's a re-render caused by a change in the state of the parent component, React will optimize the re-render for you by knowing that the props has to be referencing the same element before and after the render. This will also prevent you from having to wrap your components with React.memo everywhere in your project.

## React memo

```react
import ParentTwo from "./components/Optimization/ParentTwo";

function App() {
  return (
    <div className="App">
      <ParentTwo />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import ChildTwo from "./ChildTwo";

const ParentTwo = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  console.log("ParentTwo Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      <ChildTwo name={name}/>
    </div>
  );
};

export default ParentTwo;
```

```react
const ChildTwo = () => {

  console.log("ChildTwo Render");

  return <div>ChildTwo component</div>;
};

export default ChildTwo;
```

On page load we can see the two log messages for initial render. Now clicking the second button updates the state of the parent component. The state is passed as a prop to the child component. Both the components have to re-render and hence we see both log messages in the console. Now before we proceed just one small detail, often we're used to saying that the child component re-render because of change in props like in this instance but the fact is the child component re-rendered because the parent component re-rendered, not because the props changed. If you remove the name prop the child component still re-renders when the name state changes in the parent component. React doesn't case whether props changed, it'll always render child components just because the parent rendered.

Now when clicking the first button we again see the log statements, the parent re-rendered and so did the child. This time however it's evident that we have an unnecessarty render.
The count state variable doesn't affect the child component in any way so if we change the count state React shouldn't re-render the child component by that I mean React shouldn't go through their Render Phase and then discard the render output, as we learned unnecessary renders do affect performance.

To optimize this rendering behavior we can let React know that it should re-render the child component only if its props change and the way to do it is using React.memo.

React.memo is a higher order component which you can use to wrap components if they render the same result given the same props. Doing so will give you a performance boost in some cases by memoizing the render output. So if your component props don't change between renders React will skip rendering the component and reuse the last rendered result. In our case
React will skip rendering the child component and we use the render output from the previous render. So in the ChildTwo component all we have to do is change the export and don't forget replacing the import in the ParentTwo component:

```react
import React from "react";

const ChildTwo = () => {
  console.log("ChildTwo Render");

  return <div>ChildTwo component</div>;
};

export const MemoizedChildTwo = React.memo(ChildTwo);
```

On browser page load we get the two log messages from initial render. Click on "CHANGE NAME" button and both components render as expected. Finally click on "Count" button and you can see only ParentTwo component renders. ChildTwo doesn't render because React is using de memoized result since the child component props never changed. Now one point to keep in mind is that memo only does a shallow comparison of the previous and new props. However you can pass in a custom comparison function as the second argument to React.memo to meet your requirements.

- In React, when a parent component renders, a child component might un-necessarily render.
- To optimize this behavior, you can use React.memo and pass in the child component.
- React.memo will perform a shallow comparison of the previous and new props and re-render the child component only if the props have changed.

## Questions on Optimization

When do I use the Same Element Reference technique and when do I use React.memo?

- Same Element Reference:

  1. When your parent component re-renders because of state change in the parent component.
  2. This technique doesn't work if the parent component re-renders because of changes in its props.

  state change? Yes

  props change? No

- React.memo

  1. When your child component is being asked to re-render due to changes in the parent's state which don't affect the child component props in anyway.

  What if the child component doesn't have any props? React.memo should still works. It's still better to go with Same Element Reference because is something React automatically provides for you and it also prevents you from having to add React.memo all over your code base.

If React.memo provides the optimization by comparing the props, why not wrap every single component with React.memo?

Why doesn't React just internally memoize every component and not expose React.memo to the developers?

> "Shallow comparison aren't free. They're O(props count) time complexity and they only buy something if it bails out. All comparisons where we end up re-rendering are wasted. Why would you expect always comparing to be faster? Considering many components always get different props."

-Dan Abramov

### React.memo and hypothetical time

Let's say a component render time takes 10ms. We wanna optimize this if possible so we wrap it with React.memo, now this isn't "free", it doesn't happen in 0ms. Let's say the shallow comparison takes 2ms.

Now let's take a look at a few re-render scenarios. Let's say in our first round of re-render the props didn't change. So we have the shallow comparison of 2ms and that's it. We don't have to render the component.

- First re-render - No props change - 2ms

In the next render let's say the props changed so this time React first does the shallow comparison costing us 2ms and because the props aren't the same React renders a component which takes 10ms. Now we have taken 12ms in total to render the component.

- Second re-render - props change - 2ms + 10ms = 12ms

Provided that many components most of the time always receive new props your component rendering history could be something like this:

Initial render - 10ms

Optimized render - 2ms

Re-render - 12ms

Re-render - 12ms

Re-render - 12ms

...

So wrapping everything in React.memo can actually be detrimental to the performance of your app. Therefore is always a good idea to memoize only expensive components where the props hardly change. Another point to keep in mind is when you optimize the rendering of one component React will also skip rendering that components entire subtree because it's effectively stopping the default render children recursive bahavior of React.

## Incorrect memo with children

We tend to have components nested at various levels. So let's say the following ChildThree component in the parent component also has another child. The memoization still works fine if you're passing in a text node.

```react
import ParentThree from "./components/IncorrectOptimizations/ParentThree";

function App() {
  return (
    <div className="App">
      <ParentThree />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import { MemoizedChildThree } from "./ChildThree";

const ParentThree = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  console.log("ParentThree Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      {/*Here we're passing a text node as children prop:*/}
      <MemoizedChildThree name={name}>Hello</MemoizedChildThree>
    </div>
  );
};

export default ParentThree;
```

```react
import React from "react";

const ChildThree = ({ name, children }) => {
  console.log("ChildThree Render");

  return (
    <div>
      {children} {name}
    </div>
  );
};

export const MemoizedChildThree = React.memo(ChildThree);
```

Children components are often other HTML elements or custom components and not plain text nodes. So I'm gonna wrap the "Hello" text with a strong HTML tag. Now go to the browser and observe the memoization behavior. When you click on "Change name" button both components re-render. Now click the "Count" button to our surprise we see that both the components re-render. Even though the name value didn't change the child component re-rendered. This is because of the children props. In React props.children is always a new reference which will cause the child component to always render. So there's no need to wrap your child component with React.memo if the child component itself has children elements. In this case the incorrect memo will simply add to your component render time as new references to the children props will always caused the memoized child component to re-render.

## Incorrect memo with Impure Component

```react
import ParentFour from "./components/IncorrectOptimizations/ParentFour";

function App() {
  return (
    <div className="App">
      <ParentFour />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import { MemoizedChildFour } from "./ChildFour";

const ParentFour = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  console.log("ParentFour Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      <MemoizedChildFour name={name} />
    </div>
  );
};

export default ParentFour;
```

```react
import React from "react";

export const ChildFour = ({ name }) => {
  const date = new Date();

  console.log("ChildFour Render");

  return (
    <div>
      Hello {name}, It's currently {date.getHours()}:{date.getMinutes()}:
      {date.getSeconds()}
    </div>
  );
};

export const MemoizedChildFour = React.memo(ChildFour);
```

If you click the "Count" button in the browser only the parent component re-renders. The child component doesn't re-render because its props didn't change. This is an incorrect optimization. Ideally when there's a change in the UI we'd also want the time to get updated. This is a rare scenario but we now know it exists. So when dealing with impure components make sure you're aware of the consequences when using React.memo. By impure components I'm sort of referring to components where the JSX can change even though the props and state remain the same. Using date in the JSX is one such example and using randomness could be another example. If you're using Math.random to calculate a value which in then rendered in the JSX, React.memo might be an incorrect optimization.

## Incorrect memo with props Reference

```react
import ParentFive from "./components/IncorrectOptimizations/ParentFive";

function App() {
  return (
    <div className="App">
      <ParentFive />
    </div>
  );
}

export default App;
```

```react
import { useState } from "react";
import { MemoizedChildFive } from "./ChildFive";

const ParentFive = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  const person = {
    firstName: "Ben",
    lastName: "Z",
  };

  console.log("ParentFive Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      <MemoizedChildFive name={name} person={person} />
    </div>
  );
};

export default ParentFive;
```

```react
import React from "react";

export const ChildFive = ({ name, person }) => {
  console.log("ChildFive Render");
  return (
    <div>
      Hello {name} {person.firstName} {person.lastName}
    </div>
  );
};

export const MemoizedChildFive = React.memo(ChildFive);
```

Now when we click on the "Count" button we have both the log messages. This is unexpected since we wrapped the child component with memo and the only thing changing here is the count state variable which isn't event passed in as a prop. This is because of the person object we have in the paren component. Every time the parent component renders a new person object reference is created and then passed in as props to the child component. If the reference changed React.memo cannot optimize and hence the child also re-renders. This isn't the case with just objects, functions also have a similar effect:

```react
import { useState } from "react";
import { MemoizedChildFive } from "./ChildFive";

const ParentFive = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  const handleClick = () => {};

  console.log("ParentFive Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      <MemoizedChildFive name={name} handleClick={handleClick} />
    </div>
  );
};

export default ParentFive;
```

```react
import React from "react";

export const ChildFive = ({ name }) => {
  console.log("ChildFive Render");
  return <div>Hello {name}</div>;
};

export const MemoizedChildFive = React.memo(ChildFive);
```

### Takeaway

- If your component has objects or functions and you're wrapping one of its child components with React.memo, it's an incorrect usage of memoization.

How to make React.memo works as expected even when we have objects and functions in our react components?

## useMemo and useCallback

```react
import { useState, useMemo } from "react";
import { MemoizedChildFive } from "./ChildFive";

const ParentFive = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  const person = {
    firstName: "Ben",
    lastName: "Z",
  };

  const memoizedPerson = useMemo(() => person, []);

  console.log("ParentFive Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      <MemoizedChildFive name={name} person={memoizedPerson} />
    </div>
  );
};

export default ParentFive;
```

Now If we go to the browser and click on the "Count" button, we see only the parent component renders. We have optimized the rendering of the child component with respect to new object references between re-renders. Next let's take a look at fixing new function references between re-renders.

```react
import { useState, useCallback } from "react";
import { MemoizedChildFive } from "./ChildFive";

const ParentFive = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Ben");

  const handleClick = () => {};

  const memoizedHandleClick = useCallback(() => handleClick, []);

  console.log("ParentFive Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <button onClick={() => setName("Z")}>CHANGE NAME</button>
      <MemoizedChildFive name={name} handleClick={memoizedHandleClick} />
    </div>
  );
};

export default ParentFive;
```

Now we have optimized the rendering of the child component with respect to new function references between re-renders.

So that's how to make use of useMemo and useCallback with React.memo to optimize component rendering.

## Context

- Ways to cause a re-render:

1. Component calls useState setter function or useReducer dispatch function.
2. If parent component re-renders.
3. React context.

Let's understand the rendering behavior in React with respect to the Context API.

Render Phase when we have React Context in a component tree:

Let's assume our app has five components: App component, Parent component and Child A, Child B and Child C components. The Parent component contains a count state which renders in the JSX and the same count value has to be rendered by Child C component as well. So we create a new count context in the Parent component and provide the count value. We then use the
context hook in Child C to consume that value. Let's understand how the Render Phase works in this scenario.

![Render Phase Context](/RenderPhaseContext.png)

Let's say the initial render is completed so all teh components have been rendered once. Now since the count value is a state variable in the Parent component let's say we called the setCount setter function to increment the count value. When the count value increments the component will be flagged for re-render. We know that React starts from the root component and finds the components flagged for re-render. React says that the Parent component has been flagged. When it's rendering the Parent component React says that the Parent component also renders a context provider it then checks to see if the context provider has been given a new value since wwe increment the count value the context provider indeed has been given a new value. React will make a note to re-render all the components that consume the context value. In ou example the component is Child C. So React makes its way down the component tree and when it encounters Child C it's gonna re-render that component and by now you probably know what happens in the rest of the Render and Commit Phase. This is the rendering behavior with respect to the context API.

![Render Phase Context](/contextRender.png)

Of course there will always be certain points that we have to make note of and the context API is no exception. Let's take a look at an example and see that I'm referring to.

```react
import ContextParent from "./components/Context/ContextParent";

function App() {
  return (
    <div className="App">
      <ContextParent />
    </div>
  );
}

export default App;
```

```react
// ContextParent.js
import React from "react";
import { useState } from "react";
import { ChildA } from "./ContextChildren";

export const CountContext = React.createContext();
const CountProvider = CountContext.Provider;

const ContextParent = () => {
  const [count, setCount] = useState(0);

  console.log("ContextParent Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <CountProvider value={count}>
        <ChildA />
      </CountProvider>
    </div>
  );
};

export default ContextParent;
```

```react
// ContextChildren.js
import React, { useContext } from "react";
import { CountContext } from "./ContextParent";

export const ChildA = () => {
  console.log("Child A Render");
  return (
    <>
      <div>Child A</div>;
      <ChildB />
    </>
  );
};

export const ChildB = () => {
  console.log("Child B Render");
  return (
    <>
      <div>Child B</div>
      <ChildC />
    </>
  );
};

export const ChildC = () => {
  const count = useContext(CountContext);

  console.log("Child C Render");
  return (
    <>
      <div>Child C count = {count}</div>
    </>
  );
};
```

On page load we see the Parent and the three children render log messages. Now let's observe the render behavior when the update the count value thereby changing the context value. If you recollect from the Render Phase explanation I mentioned that if the context provider receives a new value, components consuming the context value will also get updated so in our case when I click on the "Count" button in the Parent we should see that the Parent and Child C components should re-render. However all the the four components re-render, including the others Child components which have nothing to do with the context value. This happens because, remember, when the parent component re-renders all the children components recursively re-render. This is the default bahavior in React. In fact the Child C re-renders because the parent re-render and not necessarily because the context value was updated. So what's the whole point of context? Remember context helps you solve the problem of props drilling so you don't have to specify props through each nested component. In that aspect context still remains very helpful but when it comes to context and the rendering behavior it isn't the most efficient.

There're a few ways to optimize the rendering behavior with context which we'll see.

## Context and memo

We learned about the rendering behavior with respect to the Context API.

- When the context provider is in the parent component and the parent component's state updates, every child component re-renders and not just the component consuming the context value.

Let's see what is a simple possible solution to improve the rendering behavior. We continue with the code from the [previous section](#context). Ideally what we want is only the Parent component and the Child C component to re-render. The way to achieve that is using React.memo. In the "ContextChildren.js" file apart from exporting Child A, I'm gonna export MemoizedChildA and use that as a child of the context provider.

```react
import React from "react";
import { useState } from "react";
import { MemoizedChildA } from "./ContextChildren";

export const CountContext = React.createContext();
const CountProvider = CountContext.Provider;

const ContextParent = () => {
  const [count, setCount] = useState(0);

  console.log("ContextParent Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <CountProvider value={count}>
        <MemoizedChildA />
      </CountProvider>
    </div>
  );
};

export default ContextParent;
```

```react
// Remember adding this code below ChildA component:

export const MemoizedChildA = React.memo(ChildA);
```

Let's now head to the browser and test this out. On page load we have the four log messages corresponding to the initial render, clear the console and now if we click on the button we see the log message from only the Parent and Child C components. The rendering behavior is optimized to the way we want it to behave.

What is happening here? When we click on the "Count" button the state changes which causes the parent component to re-render. Because the parent rendered React now has to re-render the child component. But the child component is wrapped with React.memo so React checks if the props have changed. Since there are no props to begin with props haven't changed and React decides it doesn't have to render the child component. Since the Child A component didn't render, Child B component has no reason to render so we don't see the log statements for Child A and Child B. Now React arrives at Child C. It sees that it consumes the count context value whose value has now changed so React proceeds to re-render Child C component. So any time we increment the count value and provide a new value to the context provider, only the component consuming that value is re-render. In our case that's the Child C component. Something you have to remember is that if the Child C component has children components, those will get re-rendered because of the default parent-child rendering behavior in React.

## Context and Same Element Reference

```react
import ContextParent from "./components/Context/ContextParent";
import { ChildA } from "./components/Context/ContextChildren";

function App() {
  return (
    <div className="App">
      <ContextParent>
        <ChildA />
      </ContextParent>
    </div>
  );
}

export default App;
```

```react
import React from "react";
import { useState } from "react";

export const CountContext = React.createContext();
const CountProvider = CountContext.Provider;

const ContextParent = ({ children }) => {
  const [count, setCount] = useState(0);

  console.log("ContextParent Render");

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count - {count}</button>
      <CountProvider value={count}>{children}</CountProvider>
    </div>
  );
};

export default ContextParent;
```

When setter function is called in the parent component React queues a re-render of the component. However React knows that re-render is caused by a state change. The component also cannot modify its own props which means the children prop couldn't have been modified. What is the children prop? It's the Child A component. So React now knows that Child A hasn't been modified and there's no need to re-render that component. This is an optimization React automatically takes care of. As React goes down the component tree it'll re-render just the Child C component as it consumes the context value whose value has been changed. So if you're using context in your React app either make sure to wrap the immediate child of the context provider with React.memo or make sure to use the children props to make use of the Same Element Reference optimization that React provides.
