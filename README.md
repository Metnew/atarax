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


#### Components
#### Constants
#### Containers
#### PWA
#### Reducers
#### Selectors
