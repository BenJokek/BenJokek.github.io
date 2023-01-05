---
title: React TypeScript Tutorial
date: 2022-12-16
categories: [React, TypeScript]
tags: [react, typeScript] # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

![img-description](/reactplustypescript.png)

<!-- _React + TypeScript_ -->

## Configuring React App

Our React App has to be configured specifically to work with TypeScript:

```shell
npx create-react-app react-typescript-tutorial --template typescript
cd react-typescript-tutorial
code .
```

Now if we expand the source folder we can see the App and index files
with a ".tsx" extension. Now components have a ".tsx" extension instead of
".js" or ".jsx".
Also everything looks the same but if you hover over App you can see that App component
is a function which returns a jsx element and this is called type inference.

![img-description](/cap1.png)

In order to keep working I'm going to remove the header content and the unnecessary imports
at the top. Also create in "src" folder a new folder named "components". There are going to be
the React components but I'll focus in TypeScript.

## Typing Props

If I try to pass a prop as normal I see an error from Typescript:

```react
// App.tsx
import { Greet } from "./components/Greet";

function App() {
  return (
    <div>
      <Greet name="Benjamin" />
    </div>
  );
}

export default App;
```

> (parameter) props: any - Parameter 'props' implicitly has an 'any' type.
> {: .prompt-danger }

```react
// Greet.tsx
export const Greet = (props) => {  //If you hover over "props" you'll see the error above.
  return (
    <div>
      <h2>Welcome {props.name}!</h2>
    </div>
  );
};
```

To inform the type of props to Typescript we use the "type" keyword, we assign a name for the type
and the object with the key (props name "name") and value (props type which is string).
Like this we have now defined the structure or type of the props that the component will
receive.
To inform TypeScript about this after props parameter within parentheses we specify colon
and then the type name.

```react
// Greet.tsx
type GreetProps = {
  name: string
}

export const Greet = (props: GreetProps) => {
  return (
    <div>
      <h2>Welcome {props.name}!</h2>
    </div>
  );
};
```

Now among all the benefits we receive, if we try to pass a number as a prop we encounter a pretty early error.

![img-description](/cap4.png)

### Props with different types and destructuring

```react
// App.tsx
import { Greet } from "./components/Greet";

function App() {
  return (
    <div className="App">
      <Greet name={"Benjamin"} messageCount={3} isLoggedIn={true} />
    </div>
  );
}

export default App;
```

```react
// Greet.tsx
type GreetProps = {
  name: string
  messageCount: number
  isLoggedIn: boolean
};

export const Greet = ({ name, messageCount, isLoggedIn}: GreetProps) => {
  return (
    <div>
      <h2>
        {isLoggedIn
          ? `Welcome ${name}! You have ${messageCount} unread messages.`
          : "Welcome guest"}
      </h2>
    </div>
  );
};
```

### Object as props

```react
// App.tsx
import { Person } from "./components/Person";

function App() {

  const personName = {
    first: "Benjamin",
    last: "Zambelli"
  }

  return (
    <div className="App">
      <Person name={personName} />
    </div>
  );
}

export default App;
```

```react
// Person.tsx
type PersonProps = {
  name: {
    first: string
    last: string
  };
};

export const Person = ({ name }: PersonProps) => {
  return (
    <div>
      {name.first} {name.last}
    </div>
  );
};
```

### Array as props

```react
// App.tsx
import { PersonList } from "./components/PersonList";

function App() {

  const nameList = [
    {
      first: "Benjamin",
      last: "Zambelli"
    },
    {
      first: "Lionel",
      last: "Messi"
    },
    {
      first: "Ricardo",
      last: "Fort"
    }
  ]

  return (
    <div className="App">
      <PersonList names={nameList}/>
    </div>
  );
}

export default App;
```

```react
// PersonList.tsx
type PersonListProps = {
  names: {
    first: string
    last: string
  }[];
};

export const PersonList = ({ names }: PersonListProps) => {
  return (
    <div>
      {names.map((name) => (
        <h2 key={name.first}>
          {name.first} {name.last}
        </h2>
      ))}
    </div>
  );
};
```

### Strings as props with specific words

```react
// App.tsx
import { Status } from "./components/Status";

function App() {

  return (
    <div className="App">
      <Status status="error"/> // Only accepts loading, success or error
    </div>
  );
}

export default App;
```

```react
// Status.tsx
type StatusProps = {
  status: "loading" | "success" | "error";
};

export const Status = ({ status }: StatusProps) => {
  let message;
  if (status === "loading") {
    message = "Loading...";
  } else if (status === "success") {
    message = "Data fetched successfully!";
  } else if (status === "error") {
    message = "Error fetching data";
  }
  return (
    <div>
      <h2>Status - {message}</h2>
    </div>
  );
};

```

### Children Props

```react
// App.tsx
import { Heading } from "./components/Heading";

function App() {

  return (
    <div className="App">
      <Heading>
        Placeholder text
      </Heading>
    </div>
  );
}

export default App;
```

```react
// Heading.tsx
type HeadingProps = {
    children: string
}

export const Heading = ({children}: HeadingProps) => {
    return <h2>{children}</h2>
}
```

### React Component as Children Props

```react
// App.tsx
import { WorldCup } from "./components/WorldCup";
import { Heading } from "./components/Heading";

function App() {
  return (
    <div className="App">
      <WorldCup>
        <Heading>Argentina wins the World Cup</Heading>
      </WorldCup>
    </div>
  );
}

export default App;
```

```react
// WorldCup.tsx
type WorldCupProps = {
    children: React.ReactNode
}

export const WorldCup = ({children}: WorldCupProps) => {
    return <div>{children}</div>
}
```

### Optional and Default Props

```react
// App.tsx
import { Greet } from "./components/Greet";
function App() {
  return (
    <div className="App">
      <Greet name="Benjamin" isLoggedIn={true} />
    </div>
  );
}

export default App;
```

```react
// Greet.tsx
type GreetProps = {
  name: string
  messageCount?: number // with "?" now is optional
  isLoggedIn: boolean
};
// messageCount has a value of 0 by default
export const Greet = ({ name, messageCount = 0, isLoggedIn }: GreetProps) => {
  return (
    <div>
      <h2>
        {isLoggedIn
          ? `Welcome ${name}! You have ${messageCount} unread messages.`
          : "Welcome guest"}
      </h2>
    </div>
  );
};
```

## Event Props

### onClick and onChange Event

```react
// App.tsx
import { Button } from "./components/Button";
import { Input } from "./components/Input";

function App() {
  return (
    <div className="App">
      <Button
        handleClick={(event) => {
          console.log("Button clicked", event);
        }}
      />
      <Input value="" handleChange={event => console.log(event)}/>
    </div>
  );
}

export default App;
```

```react
// Button.tsx
import React from "react";

type ButtonProps = {
  handleClick: (event: React.MouseEvent<HTMLButtonElement, MouseEvent>) => void;
};

export const Button = ({ handleClick}: ButtonProps) => {
  return <button onClick={(event) => handleClick(event)}>Click</button>;
};
```

```react
// Input.tsx
type InputProps = {
  value: string
  handleChange: (event: React.ChangeEvent<HTMLInputElement>) => void
};

export const Input = ({ value, handleChange}: InputProps) => {
  return <input type="text" value={value} onChange={handleChange} />;
};
```

## Style Props

```react
// App.tsx
import { Container } from "./components/Container";

function App() {
  return (
    <div className="App">
      <Container
        styles={\{ color: "orange", padding: "3rem", border: "solid" }}
      />
    </div>
  );
}

export default App;
```

```react
// Container.tsx
type ContainerProps = {
    styles: React.CSSProperties
}

export const Container = ({styles}: ContainerProps) => {
    return <div style={styles}>Placeholder text</div>
}
```

## Importing Types

Until now I've been declaring the types above components but you can also import them. This could be useful to avoid repetition.

```typescript
// Person.types.ts
export type PersonProps = {
  name: {
    first: string;
    last: string;
  };
};
```

```react
// Person.tsx
import { PersonProps } from "./Person.types";

export const Person = ({ name }: PersonProps) => {
  return (
    <div>
      {name.first} {name.last}
    </div>
  );
};
```

## useState Hook

I didn't write a single line of TypeScript code explicitly but anyways this is a completely valid function component. Because _type inference_ if you try to change for example the "false" value by "0" in the handleLogout function, an error arise.

```react
// LoggedIn.tsx
import { useState } from "react";

export const LoggedIn = () => {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const handleLogin = () => {
    setIsLoggedIn(true);
  };
  const handleLogout = () => {
    setIsLoggedIn(false);
  };
  return (
    <div>
      <button onClick={handleLogin}>Login</button>
      <button onClick={handleLogout}>Logout</button>
      <div>User is {isLoggedIn ? "logged in" : "logged out"}</div>
    </div>
  );
};
```

### useState Future Value

Now we have to expecify type explicitly and not rely in type inference. useState has the default value of "null" because by default the user is not logged in and when they log in setState has to accept an object with the user data. We use the type AuthUser for that. TypeScript will complain if we don't write the "?" to mark the value as optional, because sure it can be "null". (This is a hard coded example).

```react
// User.tsx
import { useState } from "react";

type AuthUser = {
  name: string
  email: string
};

export const LoggedIn = () => {
  const [user, setUser] = useState<null | AuthUser>(null);

  const handleLogin = () => {
    setUser({
      name: "Benjamin",
      email: "benjamin@example.com",
    });
  };
  const handleLogout = () => {
    setUser(null);
  };

  return (
    <div>
      <button onClick={handleLogin}>Login</button>
      <button onClick={handleLogout}>Logout</button>
      <div>User name is {user?.name}</div>
      <div>User email is {user?.email}</div>
    </div>
  );
};
```

### useState Type Assertion

We could also ly to TypeScript saying that an empty object is of type AuthUser with the keyword "as". If you're confident that user will be initialized soon after setup and will always have a value after, you can use type assertion. If not just leave it like before.

```react
// User.tsx
import { useState } from "react";

type AuthUser = {
  name: string
  email: string
};

export const LoggedIn = () => {
  const [user, setUser] = useState<AuthUser>({} as AuthUser);

  const handleLogin = () => {
    setUser({
      name: "Benjamin",
      email: "benjamin@example.com",
    });
  };

  return (
    <div>
      <button onClick={handleLogin}>Login</button>
      <div>User name is {user.name}</div>
      <div>User email is {user.email}</div>
    </div>
  );
};
```

## useReducer Hook

```react
// Counter.tsx
import { useReducer } from "react";

type CounterState = {
  count: number
};

type UpdateAction = {
  type: "increment" | "decrement"
  payload: number
};

type ResetAction = {
  type: "reset"
};

type CounterAction = UpdateAction | ResetAction; /*This is discriminated unions in TypeScript
and is the recommended approach for typing reducer functions*/

const initialState = { count: 0 };

function reducer(state: CounterState, action: CounterAction) {
  switch (action.type) {
    case "increment":
      return { count: state.count + action.payload };
    case "decrement":
      return { count: state.count - action.payload };
    case "reset":
      return initialState;
    default:
      return state;
  }
}

export const Counter = () => {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "increment", payload: 10 })}>
        Increment 10
      </button>
      <button onClick={() => dispatch({ type: "decrement", payload: 10 })}>
        Decrement 10
      </button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </>
  );
};
```

## useContext Hook

```react
// App.tsx
import { ThemeContextProvider } from "./components/context/ThemeContext";
import { Box } from "./components/context/Box";

function App() {
  return (
    <div className="App">
      <ThemeContextProvider>
        <Box />
      </ThemeContextProvider>
    </div>
  );
}

export default App;
```

```typescript
//theme.ts
export const theme = {
  primary: {
    main: "#1c5251",
    text: "#fff",
  },
  secondary: {
    main: "#2511c5",
    text: "#f2232c",
  },
};
```

```react
//ThemeContext.tsx
import { createContext } from "react";
import { theme } from "./theme";

type ThemeContextProviderProps = {
  children: React.ReactNode;
};

export const ThemeContext = createContext(theme);

export const ThemeContextProvider = ({children}: ThemeContextProviderProps) => {
  return (
    <ThemeContext.Provider value={theme}>{children}</ThemeContext.Provider>
  );
};
```

```react
//Box.tsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

export const Box = () => {
  const theme = useContext(ThemeContext);
  return (
    <div
      style={\{
        backgroundColor: theme.primary.main,
        color: theme.secondary.text,
      }}
    >
      Theme context
    </div>
  );
};
```

### useContext Future Value

```react
// App.tsx
import { UserContextProvider } from "./components/context/UserContext";
import { User } from "./components/context/User";

function App() {
  return (
    <div className="App">
      <UserContextProvider>
        <User />
      </UserContextProvider>
    </div>
  );
}

export default App;
```

```react
// UserContextProvider.tsx
import { useState, createContext } from "react";

type AuthUser = {
  name: string
  email: string
};

type UserContextType = {
  user: AuthUser | null
  setUser: React.Dispatch<React.SetStateAction<AuthUser | null>>
};

type UserContextProviderProps = {
  children: React.ReactNode;
};

// export const UserContext = createContext<UserContextType | null>(null)
export const UserContext = createContext({} as UserContextType);

export const UserContextProvider = ({ children }: UserContextProviderProps) => {
  const [user, setUser] = useState<AuthUser | null>(null);
  return (
    <UserContext.Provider value={\{ user, setUser }}>
      {children}
    </UserContext.Provider>
  );
};

```

```react
// User.tsx
import { useContext } from "react";
import { UserContext } from "./UserContext";

export const User = () => {
  const userContext = useContext(UserContext);
  const handleLogin = () => {
    // if (userContext) {
    userContext.setUser({
      name: "Benjamin",
      email: "benjamin@example.com",
    });
    // }
  };
  const handleLogout = () => {
    // if (userContext) {
    userContext.setUser(null);
    // }
  };
  return (
    <div>
      <button onClick={handleLogin}>Login</button>
      <button onClick={handleLogout}>Logout</button>
      <div>User name is {userContext.user?.name}</div>
      <div>User email is {userContext.user?.email}</div>
      {/* <div>User name is {userContext?.user?.name}</div>
      <div>User email is {userContext?.user?.email}</div> */}
    </div>
  );
};
```

## useRef Hook

### DOM references

```react
// DomRef.tsx
import { useRef, useEffect } from "react";

export const DomRef = () => {
  const inputRef = useRef<HTMLInputElement>(null!);
  // Without "HTMLInputElement" error: Property 'focus' does not exist on type 'never'
  // We're referencing an input html element, thats because the "HTMLInputElement" type.
  // If you're sure your reference is never null when you access it, you can add
  // the not null assertion when invoking useRef.
  useEffect(() => {
    inputRef.current.focus(); // If you don't use not null assertion you'll need to use "?" after current.
  }, []);

  return (
    <div>
      <input type="text" ref={inputRef} />
    </div>
  );
};
```

### MUTABLE references

```react
// MutableRef.tsx
import { useState, useRef, useEffect } from "react";

export const MutableRef = () => {
  const [timer, setTimer] = useState(0);
  const intervalRef = useRef<number | null>(null);

  const stopTimer = () => {
    if (intervalRef.current) window.clearInterval(intervalRef.current);
  };

  useEffect(() => {
    intervalRef.current = window.setInterval(() => {
      setTimer((timer) => timer + 1);
    }, 1000);
    return () => {
      stopTimer();
    };
  }, []);

  return (
    <div>
      HookTimer - {timer} -
      <button onClick={() => stopTimer()}>Stop timer</button>
    </div>
  );
};
```

## Class Component

```react
// App.tsx
import { Counter } from "./components/class/Counter";

function App() {
  return (
    <div className="App">
      <Counter message="The count value is "/>
    </div>
  );
}

export default App;
```

```react
// Counter.tsx
import { Component } from "react";

type CounterProps = {
  message: string;
};

type CounterState = {
  count: number;
};

// If your component doesn't have props, you could write "Component<{}, CounterState>"
// or just "Component<CounterProps>" if there is no state

export class Counter extends Component<CounterProps, CounterState> {
  state = {
    count: 0,
  };

  handleClick = () => {
    this.setState((prevState) => ({ count: prevState.count + 1 }));
  };
  render() {
    return (
      <div>
        <button onClick={this.handleClick}>Increment</button>
        <p>{this.props.message} {this.state.count}</p>
      </div>
    );
  }
}
```

## Component Prop

```react
// App.tsx
import { Private } from "./components/auth/Private";
import { Profile } from "./components/auth/Profile";

function App() {
  return (
    <div className="App">
      <Private isLoggedIn={true} component={Profile}/>
    </div>
  );
}
export default App;
```

```react
//Profile.tsx
export type ProfileProps = {
  name: string;
};

export const Profile = ({ name }: ProfileProps) => {
  return <div>Private profile component. Name is {name}</div>;
};
```

```react
//Login.tsx
export const Login = () => {
  return <div>Please login to continue</div>;
};
```

```react
//Private.tsx
import { Login } from "./Login";
import { ProfileProps } from "./Profile";

type PrivateProps = {
  isLoggedIn: boolean;
  component: React.ComponentType<ProfileProps>;
};

export const Private = ({ isLoggedIn, component: Component }: PrivateProps) => {
  if (isLoggedIn) {
    return <Component name="Benjamin" />;
  } else {
    return <Login />;
  }
};
```

## Restricting Props

```react
//App.tsx
import { RandomNumber } from "./components/restriction/RandomNumber";
function App() {
  return (
    <div className="App">
      <RandomNumber value={19} isPositive /> {/*isPositive is inferred to be true */}
      <RandomNumber value={19} isPositive isNegative Zero /> {/*ERROR*/}
    </div>
  );
}

export default App;
```

```react
//RandomNumbers.tsx
type RandomNumberType = {
  value: number
};

type PositiveNumber = RandomNumberType & {
  isPositive: boolean
  isNegative?: never
  isZero?: never
};

type NegativeNumber = RandomNumberType & {
  isNegative: boolean
  isPositive?: never
  isZero?: never
};

type Zero = RandomNumberType & {
  isZero: boolean
  isPositive?: never
  isNegative?: never
};

type RandomNumberProps = PositiveNumber | NegativeNumber | Zero;

export const RandomNumber = ({
  value,
  isPositive,
  isNegative,
  isZero,
}: RandomNumberProps) => {
  return (
    <div>
      {value} {isPositive && "positive"} {isNegative && "negative"}
      {isZero && "zero"}
    </div>
  );
};

```

## Template Literals and Exclude

```react
// App.tsx
import { Toast } from "./components/templateLiterals/Toast";
function App() {
  return (
    <div className="App">
      <Toast position="right-center"/>
      <Toast position="center"/> {/* We can write just center */}
      <Toast position="center-center"/> {/* ERROR */}
    </div>
  );
}

export default App;
```

```react
//Toast.tsx
type HorizontalPosition = "left" | "center" | "right";
type VerticalPosition = "top" | "center" | "bottom";

type ToastProps = {
  position:
    | Exclude<`${HorizontalPosition}-${VerticalPosition}`, "center-center">
    | "center";
};

export const Toast = ({ position }: ToastProps) => {
  return <div>Toast Notification Position - {position}</div>;
};
```

## Wrapping HTML Elements

```react
// App.tsx
import { CustomButton } from "./components/html/Button";
function App() {
  return (
    <div className="App">
      <CustomButton variant="primary" onClick={() => console.log("Clicked")}>
        Primary Button
      </CustomButton>
    </div>
  );
}

export default App;
```

```react
//CustomButton.tsx
type ButtonProps = {
  variant: "primary" | "secondary"
  children: string
} & Omit<React.ComponentProps<"button">, "children">;

export const CustomButton = ({ variant, children, ...rest }: ButtonProps) => {
  return (
    <button className={`class-with-${variant}`} {...rest}>
      {children}
    </button>
  );
};
```

## Extracting a Components Prop Types

You might want to reuse a components props types but you don't have acces to the type itself, perhaps it is from a library that you don't have access to. In such cases you can extract prop types of an existing component. This component needs the exact same props as the Greet component. It accepts three props: name, optional messageCount and isLoggedIn Although we could export greed props, let's assume we can't. In this case we can use React.ComponentProps to extract the props of the [Greet component](/posts/React-TypeScript-Tutorial#optional-and-default-props).

```react
// CustomComponent.tsx
import { Greet } from "/Greet";

export const CustomComponent = (props: React.ComponentProps<typeof Greet>) => {
  return <div>{props.name}</div>;
};
```

## Polymorphic Components

```react
// App.tsx
import { Text } from "./components/polymorphic/Text";
function App() {
  return (
    <div className="App">
      <Text as="h1" size="lg">
        Heading
      </Text>
      <Text as="p" size="md">
        Paragraph
      </Text>
      <Text as="label" htmlFor="someId" size="sm" color="secondary">
        Label
      </Text>
    </div>
  );
}

export default App;
```

```react
//Text.tsx
type TextOwnProps<E extends React.ElementType> = {
  size?: "sm" | "md" | "lg"
  color?: "primary" | "secondary"
  children: React.ReactNode
  as?: E
};

type TextProps<E extends React.ElementType> = TextOwnProps<E> &
  Omit<React.ComponentProps<E>, keyof TextOwnProps<E>>

export const Text = <E extends React.ElementType = "div">({
  size,
  color,
  children,
  as,
}: TextProps<E>) => {
  const Component = as || "div";
  return (
    <Component className={`class-with-${size}-${color}`}>{children}</Component>
  );
};
```
