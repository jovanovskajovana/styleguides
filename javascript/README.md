# JavaScript Code Guidelines

JavaScript has become a very powerful and popular programming language. From frontend to backend, mobile apps, etc. it seems like everyone is building their own framework and each new tool is better than the previous one. All this makes it difficult for developers to choose what to use for their next project and how they decide.

That being said, as part of a large team and an even bigger community, ask yourself:

- Are you going to work on the project alone or with another engineer?
- What are your teammates learning and using?
- What does the client prefer?
- What’s the timeline for the project?
- Are you experienced enough in that framework or technology?
- Are the docs excellent?
- How's the community?

Whatever you choose, do not take it for granted, be open to change and further learning.

## Index

- [General](#general)
  - [Code formatting & linting](#code-formatting--linting)
  - [ECMAScript](#ecmascript)
  - [Type Checkers](#typescript)
  - [IDEs and Text editors](#ides-and-text-editors)
- [Frontend](#frontend)
  - [React](#react)
  - [React Native](#react-native)
  - [Angular](#angular)
  - [Svelte](#svelte)
  - [Vue](#vue)
- [Backend](#backend)
  - [Node](#node)

## General

There are many setups and tools that are important no matter what type of application you are building. At least try to follow the ones listed below.

### Code formatting & linting

One thing that’s really important for writing better code is good linting. If you have a good set of linting rules, your code editor will be able to catch anything that could potentially cause a problem in the code.

Head over to [ESLint](http://eslint.org/) and set up a linting utility. If you are new to this or haven't yet established your own combination of rules, here is a good starting point.

Install the required dependencies:

```
yarn add -D eslint babel-eslint prettier eslint-config-prettier eslint-plugin-prettier husky
```

Then customize project's .eslintrc.js file:

```
module.exports = {
  extends: ['prettier'],
  parser: 'babel-eslint',
  parserOptions: {
    ecmaVersion: 2020,
    ecmaFeatures: {
      jsx: true,
      modules: true,
    },
  },
  env: {
    browser: true,
    node: true,
  },
  rules: {
    'no-console': 0,
    'import/prefer-default-export': 0,
    'quotes': [2, 'single', {'avoidEscape': true, 'allowTemplateLiterals': true}],
    'prettier/prettier': [
      'error',
      {
        'trailingComma': 'es5',
        'singleQuote': true,
        'printWidth': 80,
        'endOfLine': 'lf',
        'semi': false,
        'tabWidth': 2,
      }
    ],
  },
  plugins: ['prettier'],
}
```

Notice that we included [Prettier](https://github.com/prettier/prettier) in the setup. It's a matter of taste, but it can help to automatically format code and avoid errors. You can use Prettier directly in the editor, as [Prettier VS Code plugin](https://github.com/prettier/prettier-vscode), or if you're not using VS Code, check out the Prettier docs for [install instructions](https://prettier.io/docs/en/install.html) or [other editor integrations](https://prettier.io/docs/en/editors.html).

Now that you have Prettier installed in the development environment, make sure it doesn't conflict with ESLint and adjust the default setting in the code editor, like so:

```
{
  "editor.formatOnSave": true,
  "[javascript]": {
      "editor.formatOnSave": false
  },
  "[typescript]": {
      "editor.formatOnSave": false
  },
  "prettier.singleQuote": true,
  "eslint.alwaysShowStatus": true,
  "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true
  }
}
```

A final touch would be a husky lint on-prepush hook, to eliminate any linting errors before pushing the code to version control.

```
"husky": {
  "hooks": {
    "pre-push": "yarn lint"
  }
}
```

### ECMAScript

Whether you call it JavaScript, JS, ECMAScript or ES2020 is another version of the same language. The official name of the language specified by TC39 and formalized by the ECMA standards body is ECMAScript. Since 2016 the official language name has also been suffixed by the revision year, as of this writing, that's ECMAScript 2020.

But not only the name is changed with each following year. TC39, the technical steering committee, meet regularly to vote on any agreed changes on JS's syntax and behavior, which are then defined in the [ES specification](https://www.ecma-international.org/ecma-262/10.0/).

This doesn't mean that you need to learn many languages, one of the fundamental principles of JavaScript is to maintain backward compatibility, but for the sake of readability, maintenance and good practices, use the latest ECMAScript that you can afford.

Just don't forget to include [Babel](https://babeljs.io/) compiler to get the latest of the language without worrying about the older browsers' engines.

### Type Checkers

To prevent compile time errors and make it easier for future updates, some of us have decided to use [TypeScript](https://www.typescriptlang.org/docs/home.html) static typing. Anyway, relaying only on its strict syntactical superset won't make you a better developer, you still have to know JavaScript deeply and use TypeScript as a guide to do things better so that the JS engine has the best chance of efficiently running the code and avoid some of the more problematic mistakes that slip by in non-strict mode.

As for Linting, you can still refer to the standard setup [as shown above](#code-formatting--linting) and add `@typescript-eslint/parser @typescript-eslint/eslint-plugin` to the dependencies list. Prefer it over [TSLint](https://palantir.github.io/tslint/) due to Microsoft’s [plan to deprecate TSLint](https://github.com/microsoft/tslint-microsoft-contrib/issues/876) and focus on improving ESLint’s TypeScript support.

### IDEs and Text editors

As a large team of developers, each of us has an IDE / editor, a set of plugins and rules to bet on. A great part of the JavaScript team has [Visual Studio Code](https://code.visualstudio.com/) as a first choice, but there are also developers who prefer [WebStorm](https://www.jetbrains.com/webstorm/) or [Atom](https://atom.io/).

In any case, there are some setups that you need to keep in mind, regardless of your IDE / editor of choice.

- Auto formatter: [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
- Linter error display: [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
- Test results display: [Jest](https://marketplace.visualstudio.com/items?itemName=Orta.vscode-jest)

We keep track of all useful plugins and information related to IDEs and Text editors in [a sepparate section](../../IDEs) of the code guidelines.

## Frontend

### React

With its good documentation and strong community, [React](https://reactjs.org/) is our preferred JavaScript library and we have been using it since the get-go. It is constantly evolving towards better performance and simpler setup, that can be sometimes confusing for newcomers as well as professionals. Dive into our [React guideliness](./react) for a clearer picture.

### React Native

Our framework of choice for cross-platform mobile development is [React Native](https://facebook.github.io/react-native/). It enables us to work on mobile apps using React and still have native platform capabilities.

### Angular

[Angular](https://angular.io/docs), which has replaced AngularJS, is the other popular SPA framework. When you get to know it, you see that it offers a lot. With its own CLI, debug utilities, performance tools and much more it can be a powerful framework of choice.

### Svelte

[Svelte](https://svelte.dev/) is something we are still experimenting with and learning as our next possible stack. If you are interested in expanding your knowledge into it until we become fully comfortable, see Svelte [documentation](https://svelte.dev/) and [this step-by-step comparison with React](https://medium.com/javascript-in-plain-english/i-created-the-exact-same-app-in-react-and-svelte-here-are-the-differences-c0bd2cc9b3f8).

### Vue

[Vue.js](https://vuejs.org/) would be our last - but not least - framework of choice. As said above, we should be flexible about changes and client requirements, so if you decide to work with Vue, follow its [guide](https://vuejs.org/v2/guide/) and [NuxtJS](https://nuxtjs.org/) to set up a powerful modular architecture and server side rendering.

## Backend

### Node

Node is the de-facto framework for backend JavaScript development. On top of that we add [Nest.js](https://nestjs.com/), which offers a lot of powerful mechanisms and is, we believe, a fantastic framework.
