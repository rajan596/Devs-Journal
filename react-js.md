# Introduction
 
JS library, Created by Facebook to solve syncing problem for ghost messaging. THis helps greatly to keep all component states in sync.  
Used by Twitter, Netflix, Airbnb, PayPal
 
**Why** : 
Hype, Job, Trend, Build UI, Makes easy to manege complext front end

# References:
- https://react.dev/learn

# Roadmap
1. Core: State, UI Manipulation, JSX (HTML in JS)
2. Component Re-usability
3. Reusing of component - props
4. How to propogate change (hooks)

Additionals (Not react but useful with react): 
1. Router
2. State management by other lib like Redux, Redux toolkit
3. Class based component - legacy code

# Installation

Web requires =>  React + React-Web
Mobile requires => React + React-native

```
# create project and download basic dependencies
npx create-react-app myfirstapp

# Run pre-build project
npm run start

# Install ESLint plugin

# add eslint-plugin-react-hooks https://www.npmjs.com/package/eslint-plugin-react-hooks
npm install eslint-plugin-react-hooks --save-dev

# update package.json
```js
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest",
      "plugin:react-hooks/recommended"
    ]
  }

```
Project will start at http://localhost:3000/

# React Internals

- React creates its own DOM and based on need basis and diff of browser DOM and internal DOM it manipulated browser DOM.
- We can return only one element in JSX method.
- Currently React Fiber is used to update virtual DOM

## Components
- Component: piece of UI that has its own logic and appearance. Like button, page
- Its reusable piece of UI
- Components are used to render, manage and update UI element
- React Component: Java Script functions that return markup.
- One component can be nested into another component.
- React component name starts with capital letter while html tag starts with small letter.

```
function MyButton(){
    return (<button>Button Example</button>);
}
```

## JSX 
- JSX: JavaScript with HTML
- Its stricter than HTML. Tags need to be closed like <br />
- *One JSX method can't return multiple tags. It needs to be enclosed in parent tag.*


## CSS With React
- Specify class using className in react. Equivalent to HTML *class*
```
HTML =>     <img class="MyClass">
React =>    <img className="MyClass">
```

## Displaying data
- use curly braces
```js
return (
    <p> This is {user.name} </p>
);
```

## Responding to events
declare method in component and link it using onClick React property.
```js
function MyComponent (){

    function handleClick(){
        alert("Clicked");
    }

    return (
        <p onClick={handleClick}>Paragraph</p>
    );
}
```


## Hooks
- Functions starting with `use` are called **Hooks**
- React gives multiple Hooks like useState, useMemo, useEffect which does specific kind of Job
 
https://www.freecodecamp.org/news/full-guide-to-react-hooks
https://www.freecodecamp.org/news/how-to-manage-state-in-a-react-app/

### useState Hook: Remember Me ! State handling
- using useState -> ReactJS reacts with state updates. If a single counter state is used in multiple UI elemements all the components gets updated with the state change.
- If we want any component to remember something. Use *useState* from react.
- Change in state make a React component to Re-render.
- setState function is **asynchronous**. So, if we try to read the state immediately it wont be updated.
- setState method can take a callback as well like.... setState( prevState => 1 + prevState)

```js
import {useState} from 'react';

function AisUiHeading(){

// access count and manipulate using setCountMethod(newCountVal)
  let [count, setCount] = useState(0);

  function handleClick(){
    setCount(count + 2);
  }

  return (
    <>
      <h1 onClick={handleClick}>Heading: Click here</h1>
      <p>Current count is : {count}</p>
    </>
  );
}
```

#### Data Sharing between components using useState Hook
- State is scoped inside created components and cant be shared across components.
- However state can be **passed** from top to bottom component.
- The information we pass down like this is called `props` or properties or arguments.

```js
function MyTopComponent(){
// Initialize state here
let [count, useCount] = useState(0);
return (
    <>
        <MyBottomComponent count={count}>
        <MyBottomComponent count={count}>
    </>
);
}

function MyBottomComponent({count}){
    return (
        <p> Current count is {count} </p>
    );
}
```

### useCallback
- **Used to improves React components performance : Prevents unnecessary function creation on each re-renders and Returns memoized function**
- In React a component's function gets re-created for each re-rendering which means a new instance needs to be created.
- Now consider a scenario where a Hash function calculates hash based on *length, isChar, isNum* state variables. Hash method should be called when one of the state changes. There is one more field which updates some different state and which re-renders the component. So, one thing is sure that since any of the dependencies of Hash function is not updated so this function is not required to be created again.
- In above scenarios a function can be cached based on its dependencies. If one of the dependency gets updated then only function is created again.
- Thus, Preventing function creation again which results in **Performance Boost**.
- What useCallback does is to hold on to the value of the function despite the parent component re-rendering. This means that the child prop will remain the same as long as the function value remains the same as well. And that **solves the problem of unnecessary child re-rendering.**


```js
const memoisedHashFunction = useCallback(generateHashFunction, [length, includeChar, includeNum]);
```

> In short this is methos caching based on its dependency values

**When not to use useCallback ?**
- when function needs to be recreated on each rendering. Like Button click handler. In such scenarios this will degrade the performance 


### useEffect
- Allows to run a side effect once component renders. Hence name Effect.
- It allows to perform additional code execution in component like timer, fetching data to fill the DOM etc.
- Can also be used to do initial setup like listening to browser event and registering handler.
- useEffect is a tool that lets us interact with the outside world but not affect the rendering or performance of the component that it's in.
- helps to synchronize a component with an external system
- setup method can optionally return cleanup methos in-order to flush the resources like timer cleanup, connection end.
- Its a Hook, so we can only call it at top level of component not inside loops or conditions.
- If useEffect hook is used at multiple places then it can be wrapped inside customHook which can be used everywhere.

Refs:
- https://react.dev/reference/react/useEffect
- https://freecodecamp.org/news/react-useeffect-absolute-beginners/

```js
Syntax: useEffect(setup, dependencies);

/* Can be used with just callback. This will be executed on each re-renders*/
useEffect(()=>{
    // Do something
});

/* Can be used with callback and dependencies */
useEffect(()=>{
    // Execute on 1st rendering only
},[]);

// Example: Executes generateHash on 1st render and when any of the dependency (state) gets updates
useEffect(generateHash, [length, isChar, isNum]);

/* Wrapped inside custom hook */
function useMyCustomHook({a,b}){
    useEffect(()=>{
        connection.start(a,b);
        return ()=> connection.close();
    },[a,b]);
}
```

### useRef
- It returns a mutable reference object which is persisted for the full life time of component.
- It keeps track of mutable object while **re-rendering**.
- Updating reference value doesn't trigger re-rendering
- Reference update is synchronous i.e, available immediately after write while state variable update is async and is updated only after re-rendering.


### useMemo
- Returns Memoized value

### useContext
- Problem it solves: Consider a case where parent component holds a state and there are deep nesting of child component and state is passed as *props*.
- Maintaining state and passing it as props can make maintanance difficult.
- To solve this problem, useContext can be used. It provides a context where any component can get the state information.
- It's like global variable for specific use case i.e, context
- Here we will need to 1) create context and 2) access context data using useContext in react

```js
// Create context

// File: UserContext.js
// This lays down the schema what we need to store in this context.
export const UserContext = React.createContext({
  userName: "username",
  isLoggedIn : false
})

export const UserContextProvider = UserContext.Provider

/* This hook simplifies and abstracts use of useContext in components where access is required */
export function useUserData(){
  return useContext(UserContext)
}
 
// Now, this needs to be added as parent component as data provider like...
function App(){

  /*
    Here we need to pass values to context which React can then share with us using useContext.
    React just helps to store and access context data. its up us how we are storing data like using state or creating Fn
    We need to link those variables in `values` attribute of UserContextProvider which provides context to our UserContext
    This is like setting values to out Context.
  */

  return (
     <UserContextProvider value={{userName, isLoggedIn}}>
        <p>I am paragraph</p>
     </UserContextProvider>
  );
}

```

#### Other solutions
- Redux, Redux-toolkit (easier version of redux)

### useReducer
- When state is having frequent updated in that case reducer can be used to enhance performance
- Updates state based on actions. State update control is with reducer function.

### useId
- Generates random string ID 

```js
const id = useId() # Result-> :r1:
```

# Routers

> This is a broader topic and a complete information can be found on reactrouter site documentation: https://reactrouter.com/en/main/start/tutorial#the-root-route

Setup:
```bash
npm install react-router-dom
```

- `a` tag reloads entire page and re-paints. So, it should not be used in React. `Link` can be used in-place of `a` tag
- `NavLink` gives some extra features than Link.

## Approach : 1 via createBrowserRouter and RouterProvider
- We need to add routing informationwhere react is rendering data in root element.
- This will be mainly in index.js or main.js
- `Outlet` component is where child routes will be rendered. It should be used in parent route element [Ref](https://reactrouter.com/en/main/components/outlet)

```js
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <RouterProvider router={router} />
    <!--  We have removed <App /> from here because now router will decide what to render -->
  </React.StrictMode>
);

// Define router
const router = createBrowserRouter([
  {
    path:'/',
    element: <App />,
    children : [
      {
        path : 'heading',
        element : <AisUiHeading/>
      },
      {
        path : 'user/:userid', // Dynamic path params passing
        element : <UserPage/>
      }
    ]
  }
]);

// Parent component
function App() {

  return (
    <>
      <h1 className="p-4">App Component</h1>
      <ul>
        <li><Link to="/" >Home</Link></li>
        <li><Link to="/heading" >Heading</Link></li>
         <li><Link to="/user/45" >User</Link></li>
      </ul>
      <Outlet /> <-- This is where child route component will be rendered>
    </>
  );
}

```

## Approach 2 via Routes
- Using Routes component

```js
function App() {
  return (
    <Routes>
      <Route path="/" element={<Dashboard />}>
        <Route
          path="messages"
          element={<DashboardMessages />}
        />
        <Route path="tasks" element={<DashboardTasks />} />
      </Route>
    </Routes>
  );
}
```

### loader
loader in router gives flexibility to call API which is required to populate component in advance i.e, before even mounting component when user hovers on that `Link`

```js
<Route
  path="dashboard"
  element = {<Dashboard />}
  loader={methodToFetchData}
/>

/* and then use useLoaderData to fetch this data in component*/
const data = useLoaderData()

```

# Redux | Redux toolkit
- Documentation: https://redux-toolkit.js.org/introduction/getting-started
- Like `react-dom` uses `react` just like that `react-redux` uses `redux` as an internal lib.
> Redux toolkit is enhanced version of Redux which provides more abstractions and out of the box middleware support which we need to add manually in redux.
-  `Store`: single source of truth like global variable
- `Reducer`: Any change in `store` will be done by Reducer. For understanding this can be considered as Dao layer
- `useSelector`: selects value from store
- `useDispatch`: sends value to store
- `slice`: bigger version of reducer

Installation
```bash
npm install @reduxjs/toolkit
npm install react-redux
```

Setup
```js
// creating slice
const profileSlice = createSlice({
  name: 'slice-name',
  initialState, // Default values of state
  reducers: {
    addProfile : (state, action) => {
      const profile = {
        id: nanoid(),
        text: action.payload
      }
      state.profiles.add(profile);
    },
    removeProfile: (state, action)=>{},
    updateProfile: (state, action)=>{}
  }
})

export default profileSlice.reducer

// configure store. store needs to know about reducers
export const store = configureStore({
  reducer: profileReducer
})

```

##### How to create store ?
```js
import {configureStore} from '@reduxjs/toolkit'
const store = configureStore({});

// Dispatch uses reducer ans updates values in store
const dispatch = React.useDispatch()
const someHandler = () => {
  // Call reducer from here...
  dispatch(addProfile(someInput));
}

// Retrieve store data for usage
const profiles = useSelector(state => state.profiles)

// Now we need to wrap our component with Provider in index.js
<Provider store={store}>
  <App />
</Provider>
```

# Thinking in React

https://react.dev/learn/thinking-in-react

1. Break the UI in components hierarchy
Like...
```
Header
Table
    Heading
    Row
        Name
        Price
```
2. Build a static version in React
3. Find a minimal but complete representation of UI state

- Which of these are state? Identify the ones that are not:
    - Does it remain unchanged over time? If so, it isn’t state.
    - Is it passed in from a parent via props? If so, it isn’t state.
    - Can you compute it based on existing state or props in your component? If so, it definitely isn’t state!

4. Identify where your state should live ? 

- For each piece of state in your application Identify every component that renders something based on that state.
- Find their closest common parent component—a component above them all in the hierarchy.
- Decide where the state should live:
    - Often, you can put the state directly into their common parent.
    - You can also put the state into some component above their common parent.
    - If you can’t find a component where it makes sense to own the state, create a new component solely for holding the state and add it somewhere in the hierarchy above the common parent component.

5. Add Inverse data flow


# Projects

## Random string generator using useState, useCallback and useEffect

```js
import { useCallback, useEffect, useState } from 'react';
import './App.css';

function App() {

  const [length, setLength] = useState(8);
  const [isNum, setIsNum] = useState(false);
  const [isChar, setIsChar] = useState(false);
  const [hash, setHash] = useState('');

  function generateHash(){
    var s = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
    if(isChar) s+=";&@%&())";
    if(isNum) s+="0123456789";

    var hash="";
    for (let index = 0; index < length; index++) {
      const i = Math.floor(Math.random()*s.length);
      console.log("i:",i,"Length: ",length, s[i]);
      hash+=s[i];
    }
    setHash(hash);
  }

  function handleNumberCheckboxClick(){
    setIsNum((value)=>!value);
  } 

  const handleCharacterCheckboxClick = ()=>{
    setIsChar((value)=>!value);
  }

  const generateHashCb = useCallback(generateHash,[length, isChar, isNum])

  useEffect(generateHashCb, [length, isChar, isNum]);

  return (
    <div >
      <div>Result: {hash}</div>
      <div>  My length is: {length}  </div>
      <div>
        <input type="range" onClick={(e)=> setLength(e.target.value)}/>
      </div>
      <div>
        <label>Number ?</label>  <input type="checkbox" onClick={handleNumberCheckboxClick}/> 
      </div>
      <div>
        <label>Character ?</label>  <input type="checkbox" onClick={handleCharacterCheckboxClick}/> 
      </div>
      <div>
        <button 
          className="bg-slate-100"
          onClick={()=>generateHash(length, isChar, isNum)}
        >Generate Hash</button>
      </div>
    </div>
  );
}

export default App;

```