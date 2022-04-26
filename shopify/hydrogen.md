# Hydrogen Notes

Follow the instructions here to get up and running with Hydrogen and TypeScript.

**Notes last updated**: April 25, 2022

Would _highly_ recommend using `yarn` instead of `npm` at time of latest update.

## Links

- [GitHub Page](https://github.com/Shopify/hydrogen)
- [Documentation](https://shopify.dev/custom-storefronts/hydrogen)

## Setup

- Run the command: `npx create-hydrogen-app`
  - Give it a name to create a folder
  - Cannot do: `npx create-hydrogen-app .` to create in an existing directory. _Must_ create the directory as part of the setup
- After, go into the directory, open it in VS Code, install the NPM packages and start up the local dev server:

  `cd [PROJECT_NAME] && code . && yarn install yarn dev`

## Updating

### package.json

- Group all `dependencies` together alphabetically then remove `devDependencies`
- Order scripts alphabetically

### Prettier & ESLint

To use your own Prettier settings:

- Remove `"prettier": "@shopify/prettier-config",` from `package.json`
- Delete `.stylelintrc.js` file at root level
- Remove the `lint` scripts in `package.json`
- Uninstall the following packages:
  - `@shopify/prettier-config`
  - `@shopify/stylelint-plugin`
  - `prettier`
  - `stylelint`
    - Run the command: `yarn remove @shopify/prettier-config @shopify/stylelint-plugin prettier stylelint`

To use your own ESLint settings:

- Delete `.eslintrc.js` at root level
- Uninstall the following packages:
  - `eslint`
  - `eslint-plugin-hydrogen`
    - Run the command: `yarn remove eslint eslint-plugin-hydrogen`
- Start by running: `npx eslint --init`
- Answer the questions:
  - How would you like to use ESLint? · `style`
  - What type of modules does your project use? · `esm`
  - Which framework does your project use? · `react`
  - Does your project use TypeScript? · No / `Yes`
  - Where does your code run? · `browser`, `node`
  - How would you like to define a style for your project? · `guide`
  - Which style guide do you want to follow? · `airbnb`
  - What format do you want your config file to be in? · `JSON`
- Download any additional packages if prompted
- Copy and paste in rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/tree/main/rules)
- NOTE: Will still need to `import React from 'react'` due to version of `react` being used here

## TypeScript

This boilerplate project does NOT come with a TypeScript variant. We must manually convert all files to TypeScript.

### Installation

- Add `tsconfig.json` file: `tsc --init`

  - Copy and paste this code in:

  ```json
  {
    "compilerOptions": {
      "esModuleInterop": true,
      "forceConsistentCasingInFileNames": true,
      "jsx": "react",
      "module": "esnext",
      "moduleResolution": "node",
      "noEmit": true,
      "pretty": true,
      "skipLibCheck": true,
      "strict": true,
      "target": "ESNext",
      "types": ["vite/client"]
    },
    "include": ["./src", "./tests"],
    "exclude": ["./node_modules"]
  }
  ```

- We will need to instal `@types` packages for the following (from `package.json`):
  - `@types/body-parser`
  - `@types/compression`
  - `@types/eslint`
  - `@types/react`
  - `@types/react-dom`
  - `@types/serve-static`
  - `@types/tailwindcss`
- In addition to the following packages:
  - `typescript`
  - `@types/node`
- Run the command: `yarn add typescript @types/node @types/body-parser @types/compression @types/eslint @types/react @types/react-dom @types/serve-static @types/tailwindcss`

### Basic Update

- Update `src/App.server.js`

  - Rename to `App.server.tsx`
    - Will get a lot of red underlines, this is because other files haven't been converted yet
  - Remove the `session: CookieSessionStorage` in the default export
    - If you run `yarn dev`, you should see an error regarding `CookieSessionStorage`. I found removing it allows the dev server to start up ok
    - Should also get an error saying `Module '"@shopify/hydrogen"' has no exported member 'CookieSessionStorage'.`
    - Not sure why this is included then?
    - So the whole default export can be simplified to:
    ```ts
    export default renderHydrogen(App, { routes, shopifyConfig });
    ```
  - Add a props type for App:

  ```ts
  type AppProps = {
    routes: ImportGlobEagerOutput;
  };

  const App = ({ routes }: AppProps) => (
    // ...
  );
  ```

  - Update routes and default export:

  ```ts
  const routes: ImportGlobEagerOutput = import.meta.globEager('./routes/**/*.server.[jt](s|sx)');

  export default renderHydrogen(App, { shopifyConfig, routes }); // now matches order in types file
  ```

### Updating Root Level Files

- Rename `shopify.config.js` to `shopify.config.ts`
- Rename `vite.config.js` to `vite.config.ts`
  - Here, remove the `test: {}` object from `defineConfig`, as it is not a property specified in the type

### Organizing Components

- Created 2 folders in components: `client` and `server`
- Moved components into the corresponding folder
- Convert all functions to use function expression syntax

## GraphQL

- Hydrogen comes with a GraphiQL playground which you can see at `http://localhost:3000/graphql`

## Replacing Tailwind w/ Styled Components

- Steps to remove Tailwind can be found in the docs [here](https://shopify.dev/custom-storefronts/hydrogen/framework/css-support#remove-tailwind)
