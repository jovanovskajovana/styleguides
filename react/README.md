# React Code Guidelines

<img src='./resources/react.jpg' width='200'>

React is just a JavaScript library for building user interfaces. It doesn't have strict rules on how to organize and structure projects. This gives us the freedom to try different approaches and adapt those that work best. On the other hand, chasing React's - and all of React-related technologies - rapid development can bring confusion.

Here we will try to record all the basic rules and latest practices so that we can help ourselves develop faster and better projects.

## Index

- [Getting started](#getting-started)
- [File structure](#file-structure)
- [Linting](#linting)
- [The code](#the-code)
- [React dev tools](#react-dev-tools)
- [React profiler](#react-profiler)
- [Styling](#styling)
- [Hooks](#hooks)
- [Routing](#routing)
- [Server side rendering](#server-side-rendering)
- [State management](#state-management)
- [Data fetching](#data-fetching)
- [Testing](#testing)
- [Deploy](#deploy)
- [Components sharing](#components-sharing)
- [Useful components](#useful-components)

## Getting started

To get started, you will need NPM or Yarn package manager and a later version of Node set on your local development environment. Then you can choose between setting up your own Webpack and Babel, or React's officially supported build setup `create-react-app`. Either way will probably give you the same result, but from our experience, there's nothing wrong with speeding up the initial process and instead of searching for examples and boilerplates, just run `npx create-react-app app-name` and install all additional dependencies on the way.

> Note that, if you've previously installed `create-react-app` globally via `npm install -g create-react-app` it is recommend to uninstall the package using `npm uninstall -g create-react-app` to ensure that `npx` always uses the latest version.

The setup comes with a basic file structure and built-in scripts for running the app in development mode, testing and then building a production ready bundle.

## File structure

Create-react-app results to a basic project, with `.gitignore`, `package.json`, `README.md`, `yarn.lock` in it's root. It also contains `public` and `src` folders. The last one is what interests us and where we keep our source code. Everything else can be kept as it is or customized during developent.

The main focus is on the components. They can be <i>Dumb and Smart</i>, <i>Skinny and Fat</i>, <i>Pure and Stateful</i>, <i>Presentational</i> and <i>Container Components</i>. These all don't refer to a same thing, but the idea is similar - the first are concerned with how things look and the second with how things work.

Presentation components are likely to be used multiple times in container components or different pages, and it's probably a good idea to group them into a `shared` folder. Those can be <i>Page</i>, <i>Sidebar</i>, <i>Table</i>, <i>List</i> or <i>FormInput</i>. In addition to the presentation components, containers also contain all data and behavior, which they then pass on to their child components. Such example may be the <i>LoginForm</i>, <i>CrateItem</i> or <i>RemoveFromCart</i> container component.

Pages can then be considered as a special form of containers. They can be pure and just import the required containers, or include logic that needs to be passed to a few components. Anyway, they represent the main structure of the applications, as shown in the browser.

You will probably decide to use a CSS-in-JS styling solution, so the best approach would be to also split layout definitions into `styles` folder and import where needed.

```
src/
    |– __tests__                # Unit tests
    |– api/                     # API functions
    |– components/              # Different types of components
    |   |– icons/               # SVG icons
    |   |   |- Logo.jsx
    |   |   |- ArrowDown.jsx
    |   |– shared/                  # Generic components
    |   |    |- Page.jsx
    |   |    |- Header.jsx
    |   |– CreateItem.jsx
    |   |- UserList.jsx
    |
    |– constants/               # Theme definitions, variables and other constants
    |   |- theme.js
    |
    |– hooks/
    |   |- useStores.jsx        # HOC or hooks
    |
    |– pages/                   # Pages as rendered in the router
    |   |– _app.jsx
    |   |– home.jsx
    |   |– login.jsx
    |
    |– static/                  # Static resources
    |   |– images/
    |   |– fonts/
    |
    |– styles/                  # CSS layout
    |   |- GlobalStyles.js
    |   |– PageLayout.js
    |   |– InputLayout.js
    |
    |– utils/                   # Helper functions and data
    |
    |- index.jsx                # Root file that renders _app.js
```

Once the project becomes quite large, feel free to group the function-related components into sub-components that exhibit specific behaviors.

```
 |– components/
    |– User/
        |- Form.jsx
        |- List.jsx
```

Naming convention:

- UpperCamelCase for React JSX components.
- lowerCamelCase for Hooks or Higher Order Component files and folders.
- lowercase for all other root directory folders, for example: `src`, `components`, `lib`.

## Linting

A good linting setup is also key to a better React project. Check out our [JavaScript code formatting & linting](..#code-formatting--linting) and add [React ESLint Package](https://www.npmjs.com/package/eslint-plugin-react) to the existing setup.

```
yarn add -D eslint-plugin-react
```

[Airbnb’s React Style Guide](https://github.com/airbnb/javascript/tree/master/react) looks pretty much outdated regarding functional components and hooks, but there are some general rules like naming, imports, spacings, or alignment that can be still useful.

## The code

Anyway, no linter, formatter or other tool can get the best of your code unless you take care of it yourself. Remember, there is always someone who will read or work with your code, including yourself, so don't give them a hard time.

### Know when to make new components

While there are no written rules on when to move the code to a component, ask yourself:

- Is the code’s functionality becoming heavy?
- Does it represent its own thing?
- Are you going to reuse that code?

Keep in mind that the last thing anyone wants to see is a huge 200 – 300 line component full of crazy conditions.

### Use functional components over class

Functional components have slightly better performance due to how React treats functional vs class components behind the scenes. In short, when you want to render a `<User />` component, you don't care how it is defined, but for React it is important.

If `User` is a function, React needs to call it:

```
// Your code
function User({ name }) {
  return <p>Hi {name}</p>;
}

// Inside React
const result = User(props); // <p>Hi Joe</p>
```

But if User is a class, React needs to instantiate it with the `new` operator and then call the render method on the just created instance:

```
// Your code
class User extends React.Component {
  render() {
    return <p>Hi {this.props.name}</p>;
  }
}

// Inside React
const instance = new User(props); // User {}
const result = instance.render(); // <p>Hi Joe</p>
```

### Destructure all objects

This may seem unnecessary at first, but as the project grows this will save lines. Making the code dry and clean allows others to know what you are trying to access and what the object contains.

```
// Wrong
<p>{ this.props.foo }</p>

// Right
const { foo } = this.props;
<p>{ foo }</p>

// Wrong
const Page = (arguments) =>
  <PageLayout position={arguments.left}>Sidebar</PageLayout>

// Right
const Page = ({ left }) =>
  <PageLayout position={left}>Sidebar</PageLayout>
```

### Use function expressions (and fat arrow) over function declarations

There are several benefits of using function expression over function declarations besides making the code look awesome. When the js file is loaded, function declarations are hoisted to the top of the code by the browser before any code is executed, while function expressions do not hoist. This will help to enforce actually writing the function first. Also, JS sets information to variables behind the scenes, so if we think about it, it’s actually the more JS way of doing things.

```
// Wrong - function declaration
function(args) {
  // some action
};

// Right - function expression
const someFunction = function (args) {
  // some action
}


// Right - arrow function
someFunction = args => // some action
```

## React dev tools

React DevTools gives an access to the entire structure of the React app and allows you to see all the props and state that are used in the app. It is an excellent way to explore React's components tree and helps diagnose any issues in the app.

Download React DevTools for [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en), [Firefox](https://addons.mozilla.org/sl/firefox/addon/react-devtools/) or whatever browser you prefer to work on. When installed, it adds a <i>React tab</i> in the DevTools. Switching between it and <i>Elemets tab</i>, you'll be able to inspect components as they're written, and the actual DOM output.

## React profiler

You may have written amazing code, but the minute performance becomes an issue, things get a bit tricky. Before you start optimizing, make sure you are familiar with [React Profiler](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html). It cames within the React DevTools and provides some enhanced profiling capabilities in development mode.

Hit the <i>Profiler tab</i> in DevTools and start recording. DevTools will automatically collect performance data every time the application renders and will display a flamegraph with results. If it is all colored yellow or red, some improvements should be made. If you are interested in how to more efficiently retrieve data and split components, follow [this detailed article](https://marmelab.com/blog/2017/02/06/react-is-slow-react-is-fast.html).

### Memoization

[React.memo](https://reactjs.org/docs/react-api.html#reactmemo) is a higher order component used as a performance optimization tool. When a functional componen renders the same result with the same props, React will memoize, skip rendering the component and reuse the last rendered result.

## Styling

It used to be common for teams to relay on libraries such as [Bootstrap](https://react-bootstrap.github.io/) or [Material UI](https://material-ui.com/), but due to the benefits of improved tooling and CSS-in-JS approach, many teams choose to build their own component and theme libraries.

Try to create your own styled system of reusable components as so:

```
const genericMargin = styled.css`
  ${props => props.marginTop && `margin-top: ${props.marginTop};`}
  ${props => props.marginRight && `margin-right: ${props.marginRight};`}
  ${props => props.marginBottom && `margin-bottom: ${props.marginBottom};`}
  ${props => props.marginLeft && `margin-left: ${props.marginLeft};`}
  ${props => props.marginHorizontal && `margin-horizontal: ${props.marginHorizontal};`}
  ${props => props.marginVertical && `margin-vertical: ${props.marginVertical};`}
`;

// Button component
const Button = styled.button`
  ${genericMargin}
  ${genericPadding}
  ${genericFlex}
  ${genericShadow}
  ${genericText(14)}
`;
```

Use the dynamic styling benefits of [styled-components](https://styled-components.com/) to customize component's style based on its props or global theme. Styled-components has a huge community and has became a top contender for CSS-in-JS libraries.

Besides the modular style separation, declare a global style sheet that will serve as a basis for CSS and import it into the root component:

```
const GlobalStyles = createGlobalStyle`
  * {
    box-sizing: border-box;
    word-wrap: break-word;
  }

  html,
  body {
    font-style: normal;
    font-stretch: normal;
    font-family: 'Roboto'
    margin: 0;
    padding: 0;
  }

  @font-face {
    font-family: 'Roboto';
    src: url('../static/fonts) format('woff2');
  }
`;
```

For theming your app, take the advantage of the theme provider:

```
const Page = ({ children }) => (
  <ThemeProvider theme={theme}>
    <GlobalStyles />
    <Header />
    <PageLayout>{children}</PageLayout>
    <Footer />
  </ThemeProvider>
)
```

Well, we don't always build huge projects or need to share common styles across platforms. In that case we use the same approach by making it as simple as possible and splitting styles by presentation components.

```
// ButtonLayout.js
const BaseButton = styled.button`
  font-size: 12px;
  line-height: 1;
  padding: 10px 15px;
  border-radius: 2px;
  font-weight: 600;
  width: 100%;
`;

const ButtonPrimary = styled(BaseButton)`
  background-color: #6eab70;
`;

// PageLayout.js
const Layout = styled.div`
  display: flex;
  flex-direction: column;
  background: ${props => props.theme.backgroundPrimary};
  min-height: ${props => props.theme.height};
  width: ${props => props.theme.width};
  padding-left: 55px;
  padding-right: 55px;
  margin: 0 auto;
`;

// UserListLayout.js
const UserList = styled(Layout)`
  color: ${props => props.isActive ? '#000000' : '#0000008a'};
  margin-top: 100px;`
;
```

If at some point you're worried about performance of the CSS-in-JS libraries which parse and apply the styles in the browser, try [Linaria](https://linaria.now.sh/), a zero-runtime CSS-in-JS library, to extract all styles written in JavaScript code into real CSS files.

In any case, write your own, clean, and no-need-to-override styles, unless some <i>force majeure</i> requires otherwise.

## Hooks

[React Hooks](https://reactjs.org/docs/hooks-intro.html) are not even a new thing, but since they are an alternative to writing class components, we will give them a special section.

Whereas function components have been called <i>functional stateless components</i> before, they are finally able to handle their local state and side-effects with React Hooks.

### useState

Use the [useState](https://reactjs.org/docs/hooks-state.html) hook to manage local state in function components. It accepts an initial state as argument and returns, by using array destructuring, two variables - the actual state and a function to update the state by providing a new state.

```
const Video = () => {
  const [modalOpen, setModalOpen] = useState(false)

  return (
    <VideoLayout>
      <img src={videoBackground} />
      <button onClick={setModalOpen(true)}>Play</button>
      {modalOpen && (
        <ModalLayout onClick={setModalOpen(false)}>
          <iframe
            src="https://..."
            ...
          />
        </ModalLayout>
      )}
    </VideoLayout>
  )
}
```

### useEffect

Within the class components we were used to the lifecycle methods `componentDidMount()`, `componentWillUnmount()`, `componentDidUpdate()`, etc. They can be all replaced just with one hook, [useEffect](https://reactjs.org/docs/hooks-effect.html). Use it for data fetching, setting up a subscription, manually changing the DOM, or for all sorts of other side effects.

```
const [data, setData] = useState({ users: [] });
const [isLoading, setIsLoading] = useState(true)
const [isError, setIsError] = useState(false);

const fetchUsers = async () => {
  try {
    const response = await axios(url);
    setData(response.data);
  } catch (error) {
    setIsError(true);
  }
}

useEffect(() => {
  fetchUsers()
  setIsLoading(false)
}, [data])
```

### useReducer

The general rule would be to mostly use `useState` for data that stays in the component, but for major two-way data exchange between parent and child components, `useReducer` is a better choice. [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer) is handy when managing state objects with multiple sub-values or when the next state depends on the previous one.

### useContext

Common pattern of using `useReducer` is with `useContext` to avoid explicitly passing callbacks in a large component tree. Moreover, the main usage of [useContext](https://reactjs.org/docs/hooks-reference.html#usecontext) is to rerender the component with the latest consumed context value from the provider.

### useMemo and useCallback

We already mentioned [memoization](#memoization) in the context of performance improvements. While [`useMemo`](https://reactjs.org/docs/hooks-reference.html#usememo) is useful for expensive calculations, [`useCallback`](https://reactjs.org/docs/hooks-reference.html#usecallback) is useful for passing callbacks needed for optimized child components.

### Custom Hooks

See the full list of available hooks in [Hooks API Reference](https://reactjs.org/docs/hooks-reference.html) and use your imagination to expand the list by building your own [Custom Hooks](https://reactjs.org/docs/hooks-custom.html). Custom Hooks offer the flexibility of sharing logic to cover a wide range of use cases like form handling, animations, declarative subscriptions, and many more. Just, be careful to not add unnecessary abstraction too early.

## Routing

As it comes to Routing in React, we use [React Router API](https://reacttraining.com/react-router/web/example/basic) when we don't have a framework, or [Next.js](https://nextjs.org/docs/routing/introduction) server side rendering when using Next.js.

### React Router

The `react-router-dom` package includes `BrowserRouter`, `HashRouter`, and `MemoryRouter`, but for dynamic request on browser-based applications you'll need just the `BrowserRouter`. Determine the `path` of a `Route`, give it a `component` to be rendered, and then anchor it with a `Link` in the navigation or wherever you need it.

```
import { BrowserRouter, Switch, Route, Link } from 'react-router-dom'

const Navigation = () => (
  <ul>
    <li>
      <Link to="/">Home</Link>
    </li>
    <li>
      <Link to="/users">Users</Link>
    </li>
    <li>
      <Link to="/contact">Contact</Link>
    </li>
  </ul>
);

const App = () => (
  <BrowserRouter>
    <Switch>
      <Route exact path="/" component={Home} />
      <Route path="/users/:id" component={Users} />
      <Route path="/contact" component={Contact} />
      <Route component={RedirectPage} />
    </Switch>
  </BrowserRouter>
);
```

Each router creates history object to keep track of the current location of the page. Use `withRouter` HOC if you need to access the history prop and push to a new route.

### Next.js

A page in Next.js is associated with a route created by its filename. So, `pages/profile.js` will be mapped to `/profile` route. The only thing needed is a `Link` from `next/link` that will be accessible to a specific path at any time. Trough the filename, you can even utilize a dynamic route parameters, `pages/profile/[id].js`.

```
import Link from 'next/link'

const Navigation = () => (
  <ul>
    <li>
      <Link href="/">
        <a>Home</a>
      </Link>
    </li>
    <li>
      <Link href="/about">
        <a>About Us</a>
      </Link>
    </li>
  </ul>
);
```

Next.js has its own `withRouter` HOC too, and if you want to access the router object inside a function component use the `useRouter` hook.

## Server side rendering

While it required more advanced initial setups with Node.js / Express server, [Next.js](https://nextjs.org/) made it much simpler. With Next.js we don’t have to worry about bundling, minifying or hot reloading, we get a lot more features out of the box.

But, do you always need SSR? Not quite. Not all applications require SSR, especially dashboard and authentication applications that will not require SEO or sharing via social media.

Use Single-Page Applications:

- for rich site interactions,
- real-time / partial updates,
- when the network is fast,
- there are minimal server recourses,
- main scripts are small or lazy loaded.

Prefer Server-Side Rendering:

- when SEO is important,
- social sharing is important
- the network is slow,
- there are enough server recourses,
- main scripts are large and load slowly.

Keep reading our Next.js guidelines in [this separate section](../NextJS.md).

## State management

Managing states with nothing but React - `useState()` hook, or `setState()` for class components - is quite possible, but once the application scales up to complex trees, sending props to each child component becomes redundant and inefficient. That's the point when you start thinking about an additional state management solution.

### MobX

We, as Povio prefer to use [MobX](https://mobx.js.org/react-integration.html). It has a basic philosophy and architecture and lets you model your `observable` state in any data structure such as objects, arrays and classes. UI updates can then be automated with `computed` properties, or invoked by the `actions` that modify state based on a user-triggered event and other side effects.

MobX is less opinionated, causing newcomers a great deal of misunderstanding on their decision whether to use observer component, HOC observer, useObserver Hook, or perhaps stick to Class components with heavily sugested inject. As of this writing, React Hooks are most likely the easiest way to consume MobX store:

```
// src/stores/catStore.js
import { makeAutoObservable } from 'mobx'

export default class CatsStore {
  cats = ['Toby', 'Gus', 'Zoe']

  constructor() {
    makeAutoObservable(this)
  }

  addCat = cat => {
    this.cats.push(cat)
  };

  get catsCount() {
    return this.cats.length
  }
}


// src/stores/themeStore.js
import { makeAutoObservable } from 'mobx'

export default class ThemeStore {
  theme = 'light'

  constructor() {
    makeAutoObservable(this)
  }

  setTheme = newTheme => {
    this.theme = newTheme
  }
}

// src/stores/index.js
import CatStore from './CatStore'
import ThemeStore from './ThemeStore'

export default const storesContext = React.createContext({
  catStore: new CatStore(),
  themeStore: new ThemeStore(),
})

// src/hooks/useStores.js
import storesContext from 'stores'

export const useStores = () => React.useContext(storesContext)

// src/components/CatsList.js
import { observer } from 'mobx-react-lite'
import { useStores } from 'hooks/useStores'

const CatsList = () => {
  const { catStore } = useStores()

   return (
    <>
      <h1>There are {catStore.catsCount} cats.</h1>
      <ul>
        {catStore.cats.map(cat => (
          <li key={cat}>{cat}</li>
        ))}
      </ul>
    </>
  )
})

export default observer(CatsList);
```

\* If you are looking for a more opinionated alternative, see [mobx-state-tree](https://github.com/mobxjs/mobx-state-tree). MST enforces consistency, supports typed observables and is quite different from pure MobX - so there is some learning curve.

### Redux

Immutabillity and using a single store over multiple stores to save state, would be the two main differences from Mobx. [Redux](https://redux.js.org/) has a large community and support, as well as it has been around for as long state management libraries have been around. This provides many benefits. There are also some oppinons that once the application gets bigger and has multiple developers working on it, you should consider using Redux. It enforces by nature to use explicit actions to change the state, meaning you can never modify the state directly.

It's true that there is a clear way of doing things in Redux, but we wouldn't say you cannot use MobX in scaling applications. MobX comes with more internal implementation magic than Redux, which initially makes it easier to use with less code. It is also simpler to combine with local state and Context API, and allow the development team to set up their own architecture. That's why Mobx is our preferred state management tool, the moment it came into play.

### Apollo

Another library used in state management that has gained a lot of popularity also among us, is [Apollo](https://www.apollographql.com/docs/react/). We're using Apollo to manage remote data from a GraphQL API as well as to manage application's [local state](https://www.apollographql.com/docs/react/data/local-state/).

You do not need Redux or Mobx, instead use the Apollo Client cache as your single source of truth, which, in addition to the remote data, holds all local data. You can then access the local state with GraphQL queries and mutations, just like with server data.

### Relay

If we're already mentioning Apollo as a state management solution, there has to be a place for [Relay](https://relay.dev/en/) as another option with GraphQL based APIs. Relay too has it's own [local state management](https://relay.dev/docs/en/local-state-management) approach, but although it is referenced many times trough the React documentation, we find Apollo as an option-richer and better documented tool.

### Context API

The [React Context API](https://reactjs.org/docs/context.html) is officially recommended from React 16.3.0 as a way to avoid manually passing props to all tree levels, even when not needed. It is very handy for sharing data that can be considered "global" and should be accessible to many components in the tree, such as theme, preferred language, or authenticated user.

Don’t use context just for passing props down to a child component, rather use it for library code. As React’s documentation says, if you only want to avoid passing some props through many levels, [component composition](https://reactjs.org/docs/context.html#before-you-use-context) is often a simpler solution than context.

## Data fetching

And yet another section of decisions. The fifty-fifty match would be, use Axios when working with REST API, and stick with Apollo as a GraphQL client.

### Fetch and Axios

Browsers allows you to use the native fetch API, but there may be problems, especially with older ones. Once you start testing your application in a headless browser environment (where the browser is mocked), there can be issues with the fetch API.

There are a couple of ways to make fetch work in older browsers (polyfills) and in tests (isomorphic-fetch), but an alternative is to substitute the native fetch API with a stable library such as [axios](https://github.com/axios/axios), which performs asynchronous requests to remote APIs.

Go up to the [useEffect](#useEffect) section for a simple data feaching example.

### Relay vs Apollo

Do you even need a QraphQL client? No, you can get quite far by using fetch and interact with the GraphQL API directly. GraphQL APIs are commonly exposed over HTTP where queries and mutations can be sent in the body of a POST request.

However, you cannot use its cache, manage websockets for GraphQL subscriptions that provide real-time updates, or pagination for collections. The GraphQL client must come with such features so that you don’t have to implement these behaviours yourself.

[Relay](https://relay.dev/) and [Apollo](https://www.apollographql.com/) are both mature solutions. Apollo seems to be growing faster but steadily, clearly documenting each step. That's why we chose it for our projects.

## Testing

Source code testing is essential to programming and should be considered mandatory. The order would be as follows:

- use Jest for snapshot unit tests,
- Enzyme for integration tests, and
- Cypress to perform end-to-end functional testing.

## Deploy

A few free services that we're using constantly are:

- [Netlify](https://www.netlify.com/),
- [GitHub Pages](https://www.netlify.com/)

for static deployment, and:

- [Heroku](https://www.heroku.com/), or
- [Zeit](https://zeit.co/)

for static and also SSR applications. Each of them come with a web interface providing continuous deployment through GitHub. You can setup your CI and automaticaly deploy the application after each commit to master branch.

## Components sharing

There are many cases when we share reusable UI components across different segments of the project (dashboard, landing page, etc.) or across platforms (React, React Native projects). In addition to the components, we can share GraphQL queries and muttation.

Most often we use:

1. [Storybook](https://storybook.js.org/)
2. [Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
3. [Bit](https://docs.bit.dev/docs/quick-start)

## Useful components

- Svg as component
- Modal with portals
- HOC Loaders
- Animate reveal

// Todo
Link to separate repo
