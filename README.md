# atarax
Painkilling guidё about React/Redux and modern frontend development.

> Always interested in your feedback :heart:.
I'm not a native English speaker, so I'll be very grateful for every typo and mistake reported to issues or fixed by PR :smiling_imp:

## Intro
I don't think that I'm a Redux pirate or React ninja.          
Everything below is a result of my own experience inspired by different books, articles, videos and concepts.     
All concepts below are aggregated in [Suicrux](https://github.com/Metnew/suicrux).

### What to use for ...
Almost every library has a “clone” (alternative). That means that you can find more than one library to solve a specific task. That means when you see a reference to React Router be sure that there is a “clone” of this library, but for Preact.

#### Component-based library.
You can use React/Preact/Inferno. It doesn’t really matter. React is well-known and canonical. Preact is small and could be used only for production. Inferno seems to be faster than any other lib according to some benchmarks.

> But this `speed` doesn’t matter in “real-life”. 90% of speed issues caused by bad optimization of your app, not a framework. So there is no difference what library to choose(**except size**).

Must-have libraries:
- `react-router`(or another routing library),
- `react-helmet`(easily manipulate <head> tag).

#### Flux-architecture library.
Mobx/Saga/Redux/Flux/Kea  —  it doesn’t really matter.

> When you use any of these libraries — you use the same flux-architecture concept.

- `redux-thunk`, `react-redux`, etc.  —  obviously.
- `react-router-redux` —  sync your library with location. (Also, RRRv5 sometimes doesn’t work as you expect).
- `promise-middleware` —  smart solution to use promises as an action payload and evaluate them using middleware. (will cover in next article)

#### Styles & Styled components.
> “Styled components”  —  is a design approach.  “Thinking in components, styling in components”.
`Styled-components`  —  is a library to design styled components in React using HOCs

- `styled-components` —  bloated, requires good understanding of CSS methodologies. But it’s very powerful and has a rich API.
- `glamorous` —  API similar to `styled-components`, lightweight, good performance.
- `emotion`  —  API similar to `styled-components`, has “extract mode” that makes it fast.
- `glamor` —  lightweight and simple inline-css library. `glamorous` is based on `glamor`.

What’s a problem with “styled components”? 
- Sometimes hard to overwrite your CSS framework.
- There is no styling methodology, and SMACSS/BEM/etc. will not work well.
- Sometimes you could incorrectly connect your app logic with styles.

You have to design your own styling strategy to write configurable, fast, composable components. Personally, I didn’t find any new methodology for styled components, but can give you some recommendations:
- Don’t pass too many different props.
- Don’t make a styled component for every custom component in your app.
- HOCs could save you a lot of time.
- Don’t mix logic props and UI props (font size, color)

Remember that all these libraries were created to solve same issues.

#### UI
Probably, [this article with a comparison](https://hackernoon.com/the-coolest-react-ui-frameworks-for-your-new-react-app-ad699fffd651) of different UI frameworks could help you select a framework for the next project.

If you want fast UI framework then select a framework powered by library like `styled-components` or inline-css lib.

Don't know current MUI development progress, but as of July-Aug 2017:

> Don’t know current MUI development progress, but as of July-Aug 2017:
- it was(!) slow, but according to the latest news right now perf is better.
- design isn’t 100% material.

> Semantic-UI-React  —  many components out-of-box, but requires full Semantic CSS file. (548 kb ungzipped).

> “Ant design” looks great.

#### Webpack
As of 2017, your project must be universal. It's very important. Both server and client must be hot-reloaded.

> [Step-by-step guide how to achieve universality.](https://github.com/Metnew/tiny-universal-skeleton) Also, it’s an alternative to most solutions. One port and FS instead of 2 ports and different FSs.

- Compile both server and client with Webpack
- Create shared config for both server and client (e.g. file with used by both client and server configs loaders, plugins, entries)
- Extract your production and development configs in a separate module and save it for the next project.

> Frameworks(Next.js) and starters (razzle) supports custom Webpack configs (the end of old-fashion starters era is soon). Keep in mind, some project supports custom Webpack config only partly. That’s the only reason why starters are still used.

Some plugins you could have missed:
  - `circular-dependency-plugin`  —  detects circular dependencies
  - `autodll-webpack-plugin`  —  recompile only updated code, without vendor bundle
  - `write-file-webpack-plugin`  —  write files from memory FS to real FS. (e.g. write files from webpack-dev-middleware to FS)
  - `assets-webpack-plugin`  —  get webpack stats (similar to `webpack-stats-plugin`, but better)

#### Static-typing:
Flow.

Typescript is cool, modern and has more features than Flow. But keep in mind that when you use TypeScript - you dive into the TypeScript world. That means you use "another" language with own ecosystem.

When you use Flow you just create an abstraction around your dynamic-typed code and still write JavaScript.

#### ESlint:
Standard code style is :heart:.
If you care about a11y (accesibility), then use `eslint-plugin-jsx-a11y`.

> It’d be better to use `eslint-plugin-standard` than standard package, some default Standard rules are annoying, like `no-tabs`, `no-unused-vars` and `indent`.

#### Babel
Add `babel-preset-react-optimize` in production.
Don’t forget to add `styled-components` plugin, if you use `styled-components` library. 
Also, `module-resolver` plugin is very useful.


### Folder structure:

There are many approaches to structuring react/redux project. Keep in mind, that this structure may vary according to your framework, setup, etc. For example, Next.js, CRA already have some predefined folders.

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

Probably, jest_config and webpack_config dirs are obvious. So let’s take a look at the /src folder.

#### `/src/client`
Here we store code that renders the app in a browser.
This is your browser entry  —  code that renders an app in a browser, imports framework styles, polyfills, your global styles, PWA setup. It could be in `/common` folder too, but putting it into distinct folder seems more semantically correct.

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
//
const initialState = window.__INITIAL_STATE__ || {}
const propsForRoot = configureApp(initialState)
const RootComponent = configureRootComponent(propsForRoot)
render(RootComponent, document.getElementById('app'))

if (module.hot) {
	module.hot.accept()
}

```

#### `/src/server`
Your server-side code.

#### `/src/common`
That’s the place where the most interesting things happen. 
```
 /common
├── actions # Actions 
├── api # API (requests, data management utils)
├── app # App core(boot loader)
├── components # Reusable components
├── constants # Constant values, like months names, etc.
├── containers # App routes
├── pwa # Progressive Web App code (optional)
├── reducers 
├── selectors 
├── styles # Styled components + global CSS
├── types # Flow types for your app’s entities
```

### Routes (`/containers`)
Almost every route has own `/components` folder where specific for this route components are stored, i.e.:

- container = `containers/App/index.jsx`
- route’s component = `containers/App/components/SomeAppItem.jsx`

### API


**Don't mix API requests with Redux with React containers!**
Always try to distinct parts of your app into autonomous libraries.
[Principal of minimal knowledge](https://en.wikipedia.org/wiki/Law_of_Demeter).
Review on all approaches to work with async actions in Redux.



### Components

### Component vs PureComponent vs "stateless component"

[Note](https://stackoverflow.com/questions/40703675/react-functional-stateless-component-purecomponent-component-what-are-the-dif)

```javascript
if (componentHasNoState) {
  return itsStatelessComponent()
} else if (componentHasSimplePropsState && propsHasNoNestedObjects) {
  return itsPureComponent()
} else {
  return itsComponent()
}
```

Keep in mind, that change of component type doesn’t guarantee performance improve.

### Containers vs Components

Follow Dan's guide:

- **Container** - "smart" React component connected to Redux state.
- **Component** - "dumb" React component that uses only own props. Can't be connected to Redux.

<hr />

There are no practical recommendations about structuring `/components` folder, except “Atomic” approaches and other heuristics (*those have never worked for me*). Semantic UI glossary proposes similar to “Atomic” approach that works better than analogs (but still isn’t perfect).

> A component is a general term used to refer to any user interface element packaged for distribution.

### Parts

There are always components that exist in **one instance**, like Sidebar, Header, Footer, etc. Store singletons in `/parts`.

> SUI doesn’t propose “parts”.


#### Views

> A view is a common design trope for presenting specific types of website content like comments, or user activity. These are common views: comments, activity feeds, content cards, that are presented in a similar fashion across most websites.

If a component is a parent for other components, **represent some content**, and **it’s dumb** then it’s probably a View.
Summary:

- huge component
- takes a big place in UI
- dumb or not so smart to be called "Module"
- has many components inside

#### Addons

Component may be an *addon*, if this component is a **wrapper** for another component, or a component that could be perfectly replaced by a library, but was developed from scratch (*custom spinners, progress bars*).

Component is an *“addon”*, if...:
- it’s a wrapper for another component
- it is small lib implemented inside the project

#### Elements

> UI elements are the smallest atomic unit for describing page content. They can exist alone or in groups with shared qualities.

Component is an *“element”*, if…:
- small + dumb
- widely used in other components
- built from scratch

#### Collections

> Collections are heterogeneous group of several ui elements which are usually found together. They may describe ui elements which do not necessarily have to be present.

Collection is a group **of different similar components** inside itself.<br>
Collection differs from views:

- **"view"** - represents specific for sites content.
- **"collection"** - doesn’t represent specific content. Grouped different components.

#### Modules

> Modules are UI components that have behaviors as an essential part of their definition. These include things like, accordions, dropdowns, and popups, which require not only a description of how they appear, but also a set of instructions for how to implement their behaviours.

When your component has some logic inside (e.g. `onClick` handler), this component is a module. *Module is a **component** logically connected to a parent component.*

#### Behaviors

> Behaviors are instructions for UI behaviour that are not attached to a particular physical form. They may describe things like, form validation, state management, or server request routing.

Component that doesn’t exist as UI element, but brings some logic. Like components those implement animations. Example: `react-motion` and `react-fns`


#### PWA
Use Offline-Plugin. It requires zero config as long as you:
1. Don't have SSR (sometimes).
2. Don't have authorization logic that relies on location. (e.g. redirect to `/auth`, if user isn't logged in)
3. Don't have other logic that interacts with location
4. Don’t have specific requirements to caching, cache validation or anything similar that connected to offline.

If you got one of these features —  it’d be better to test your PWA setup in *different browsers with different behaviors/app flows.*

#### Reducers
>  [Dissecting Twitter’s Redux Store](https://medium.com/statuscode/dissecting-twitters-redux-store-d7280b62c6b1).
You can make your own research and dissect any popular app. Pinterest, for example.

95% of all states in any app can be interpreted as:
```ts
interface State {
	entities: object;
	fetchStatus: 'none' | 'loading' | 'loaded';
	errors: object;
}
```

The main key to Redux development:
> Your state is just an in-memory DB. 

> Don’t store things those could be computed by selectors.

#### Selectors
> Always use selectors, it’s very important!

What is a "selector"?

If your `mapStateToProps` looks like:
```js
const mapStateToProps = (state) => {
  // BAD!!!
  const {variable} = state.let.var.be.const.hello.world
  return {variable}
}
```

Then you’re doing that wrong, because:
- every time the way you compute some value changes, you have to change it across a whole app.
- every time your state structure changes, you have  to fix path to the property across a whole app.
- you connect your computational(selectional) logic with your container, container shouldn’t know about it.

Instead:
```js
import {getThatState} from 'selectors'
// ...
const mapStateToProps = (state) => {
  // GOOD!
  return {variable: getThatVariable(state)}
}
```
