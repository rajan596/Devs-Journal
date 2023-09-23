# Introduction
 
JS library, Created by Facebook to solve syncing problem for ghost messaging. THis helps greatly to keep all component states in sync.  
Used by Twitter, Netflix, Airbnb, PayPal
 
**Why** 
Hype, Job, Trend, Build UI, Makes easy to manege complext front end

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
```
Project will start at http://localhost:3000/

# React Internals

- React creates its own DOM and based on need basis and diff of browser DOM and internal DOM it manipulated browser DOM.
- We can return only one element in JSX method.