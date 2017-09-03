# atarax
Painkilling Stylёguidё for React/Redux and modern frontend development.

## Intro
I don't think that I'm a Redux pirate or React ninja. Everything below is a result of my own experience inspired by different books, articles, videos and concepts.     
All concepts below are aggregted in [Noir]().

### What to use for ...
Almost every library has "clone". That means that you can find more than one library to solve specific task.

#### React.
You can use React/Preact/Inferno. It doesn't really matter. I prefer to use React, because it's well known. Preact is cool, но если бы мне приходилось выбрать не реакт, а другую react-like library я бы выбрал Inferno из-за скорости.

Must-have libraries:
- `react-router`(or another routing library),
- `react-helmet`(easily manipulate <head> tag).

#### Redux.
You can use Mobx/Saga/Redux/Flux. It doesn't really matter, because when you use any of these libraries you use the same flux architerture concept. I always use Redux because it's dead simple and that makes it even more powerful.

Use `react-redux` to connect your react components with redux.
Use `react-router-redux` to sync redux with location.
Use `redux-thunk`! It's very important to use this library, but you're always can omit this. More detailed in Actions section.
Use `reselect`. More detailed in Selectors section.

#### Styles.
Use `styled-components` and `postcss`.      
There are a lot of "clones", like `glamour`, `glamourous`, `cxs`, JSCS, etc. All these libraries aimed to solve the same problem.     
Probably, `emotion` is the best "clone", because of extract mode that makes it very fast. I didn't find visible(!) difference in speed between styled-components and other libs.
The main benefit of using styled-components: it's very powerful and has a rich API.

#### UI framework.
Probably, [this article with comparison of UI framework will be useful for you](https://hackernoon.com/the-coolest-react-ui-frameworks-for-your-new-react-app-ad699fffd651) to select UI framework.
I don't like MUI - it's slow and looks not so good as it could be.     
I like Semantic-UI-React, because of many components out-of-box. But it's slow, because it requires full Semantic CSS file. (548 kb ungzipped).    
If you want fast UI framework then select framework powered by CSS-in-JS library like `styled-components`.

#### Webpack:
As of 2017, your project must be universal. It's very important (more info in HMR section). That means no separate server and client. Both server and client must have hot-reloading.

Must-have plugins that you probably have missed:
  - webpack-bundle-analyzer
  - circular-dependency-plugin
  - autodll-webpack-plugin
  - favicons-webpack-plugin
  - webpack-common-shake
  - webpack-stats-plugin
  - write-file-webpack-plugin

#### Static-typing:
Use Flow.

Typescript is cool, modern and has more features than Flow. But keep in mind that when you use TypeScript - you dive into the TypeScript world. That means you use another language with own ecosystem.

When you use Flow you just create an abstraction around your dynamic-typed code and still write JavaScript.
Tip: Use `flow-ide` package for Atom for better development experience.

#### ESlint:
Standard code style is :heart: - `eslint-plugin-standard`.
If you use Flow - include `eslint-plugin-flowtype`.
If you care about a11y (accesibility), then use `eslint-plugin-jsx-a11y`.

#### Babel
Use stage-1 or stage-0, but don't use legacy decorators.
If you use Flow - include `babel-preset-flow`.
Add `babel-preset-react-optimize` in production.
Add `babel-plugin-lodash` if you use lodash.

#### Development
Probably, anyone knows `redux-devtools-extension` and `react-addons-perf`. There are more interesting utils like `why-did-you-update` and `react-a11y`.

#### Test
Use Jest. It's very simple and powerful testing framework. But still it requires some additional configuration for advanced projects.

### Folder structure:
There are many approaches to structuring react/redux project.
Personally, I've found next structure as the best solution:
`/root` project's folder:
```
├── ...
├── jest_config         # Jest configuration lives here
├── src                 
|   ├── client          # Entry file for browser
│   ├── common          # Sources for both browser and server
│   └── server          # Server code
├── webpack_config
│   ├── client          # Webpack config for client
│   └── server          # Webpack config for server
├── ...
```

Probably, code inside `jest_config` and `webpack_config` is almost obvious. So let's take a look at the `/src` folder.

#### `/src/client`
Here we store code that renders the app in a browser.
This code includes import of framework styles, polyfills and app rendering in a browser.
It looks like:
```js
// Styles
import 'semantic-ui-css/semantic.css'
// Fetch and promise polyfill
import 'promise-polyfill'
import 'isomorphic-fetch'
// Application
import React from 'react'
import ReactDOM, {render} from 'react-dom'
import {configureApp, configureRootComponent} from 'common/app'

const initialState = window.__INITIAL_STATE__ || {}
const propsForRoot = configureApp(initialState)
const RootComponent = configureRootComponent(propsForRoot)
render(RootComponent, document.getElementById('app'))

if (module.hot) {
	module.hot.accept()
}

```

#### `/src/server`
Your server-side code. More about this in next sections.

#### `/src/common`
That's the place where the most interesting things happen. Unless your project isn't a Hello World app you'll care about structure of this folder.
```
/common
├── actions         # Your actions here  
├── api             # API stuff (requests, wrappers for requests)
├── app             # Main configuration stuff
├── components   
├── constants    
├── containers        
├── pwa        
├── reducers        
├── selectors   
├── styles        
├── types   

```


### Actions

Every async action must have 4 states:
- PENDING
- SUCCESS
- FAIL
- FINALLY


PENDING

### API
#### Don't mix API stuff with Redux stuff.
There are many examples where API logic is connected with Redux actions logic. For example, when people use `dispatch` directly inside the `fetch()` response:
```js
// Where `dispatch` is Redux's `dispatch` function
fetch('/hello')
  .then((res) => dispatch('SUCCESS', res))
  .catch((e) => dispatch('FAIL', e))
```
It's not recommended, because of [principal of minimal knowledge](https://en.wikipedia.org/wiki/Law_of_Demeter). Instead of this, your `fetch()` (or another API agent/service) must return value about request response status. And then this value **must be evaluated by Redux**.

Solution:
`fetch` response has `ok` property that can be used as an indicator of your request result.


### Component vs PureComponent vs "stateless component"

[Good note about it.](https://stackoverflow.com/questions/40703675/react-functional-stateless-component-purecomponent-component-what-are-the-dif)

```javascript
if (componentHasNoState) {
  return itsStatelessComponent()
} else if (componentHasSimplePropsState && propsHasNoNestedObjects) {
  return itsPureComponent()
} else {
  return itsComponent()
}
```

### Containers vs Components

Follow Dan's guide:

- **Container** - "smart" React component connected to Redux state.
- **Component** - "dumb" React component that use only own props. Can't be connected to Redux.

### Containers

Almost every container (except very-very simple) has own `/components` folder where it stores:

- components specific for this route
- main container's component ("root" component)

Example:

- container = `containers/App/index.jsx`
- specific components = `containers/App/components/SomeAppItem.jsx`
- "root" component = `containers/App/components/index.jsx`

### Components

I recommend to use components structure from Semantic.UI. I found it very useful in few projects.<br>
[Semantic.UI's glossary and explanations](http://learnsemantic.com/developing/glossary.html)

"Component" doesn't mean app's components inside containers. (e.g not `containers/**/components`)

> A component is a general term used to refer to any user interface element packaged for distribution.

### Parts

Imagine that you have `Sidebar` component configured from `App` container, then **`Sidebar` is a singleton**. Store components that **exist in a single instance in "Parts"**.          
Another example: only one instance of `Header` could live in a whole app.

#### Views

> A view is a common design trope for presenting specific types of website content like comments, or user activity. These are common views like, comments, activity feeds, content cards, that are presented in a similar fashion across most websites.

If component is a **huge**, **contains many other components** inside itself and **takes a big place in UI**, but **it's dumb**(or not so "smart" to be called module) then it's probably a "View" component. View:

Summary:

- huge component
- takes a big place in UI
- dumb or not so smart to be called "Module"
- has many components inside

#### Addons

Component is an _addon_, if this component is a commonly used component **built on top of another component**, includes **some custom code**, is a **wrapper** for another component, or just a thing that must be **delegated to a separate lib.**

Summary:

- build on top of another component (!, otherwise it's a Module)
- include some custom code (enough "smart")
- is a wrapper for another component
- small lib implemented inside the project

#### Elements

> UI elements are the smallest atomic unit for describing page content. They can exist alone or in groups with shared qualities.

Element is a small dumb component:

- small
- dumb
- used in other components
- built from scratch(not on top of other components)

#### Collections

> Collections are heterogeneous group of several ui elements which are usually found together. They may describe ui elements which do not necessarily have to be present.

Collection is a group **of different similar components** inside itself.<br>
Collection differs from views:

- "view" - is a UI component that represents specific for sites content
- "collection" - not always a UI component, not represents specific content(!)

#### Modules

> Modules are UI components that have behaviors as an essential part of their definition. These include things like, accordions, dropdowns, and popups, which require not only a description of how they appear, but also a set of instructions for how to implement their behaviors.

When your component has some logic inside (e.g. `onClick` handler) -> you need to place this component into `/modules`.<br>
Module is the thing that always has a **connection with a parent component**.

#### Behaviors

> Behaviors are instructions for UI behavior that are not attached to a particular physical form. They may describe things like, form validation, state management, or server request routing.

It's probably obvious.

Your components still is a component even if it works with redux using props. (E.g. Redux `store` is passed as props, or onClick handler is a function from mapDispatchToProps)

#### Components
#### Constants
#### Containers
#### PWA
#### Reducers
#### Selectors
