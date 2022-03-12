# Hydrogen Notes

These are just a collection of my notes from digging around with [Hydrogen](https://github.com/Shopify/hydrogen).

## Links

- [GitHub Page](https://github.com/Shopify/hydrogen)
- [Documentation](https://shopify.dev/custom-storefronts/hydrogen)

## Setup

- Run the command: `npx create-hydrogen-app`
  - Give it a name to create a folder
  - Cannot do: `npx create-hydrogen-app .` to create in an existing directory. _Must_ create the directory as part of the setup
- After, go into the directory, open it in VS Code, install the NPM packages and start up the local dev server:

  `cd [PROJECT_NAME] && code . && npm i --legacy-peer-deps && npm run dev`

## Project Contents

List of files/folders and their purpose:

- `.devcontainer`
  - a `devcontainer.json` file in your project tells VS Code how to access (or create) a development container with a well-defined tool and runtime stack
  - this container can be used to run an application or to separate tools, libraries, or runtimes needed for working with a codebase.
  - [source](https://code.visualstudio.com/docs/remote/containers)
- `.vscode`
  - settings for the current workspace (project)
  - [source](https://code.visualstudio.com/docs/getstarted/settings)
- `node_modules`
  - contains all npm packages
- `public`
  - for public files, such as a `favicon`
- `src`
  - also has the following sub folders:
    - `components`
    - `routes`
- `.eslintrc.js`
  - eslint config file
- `.gitignore`
  - file to purposely NOT track things in git
- `.npmignore`
  - used to keep stuff out of your npm package
  - if there's no `.npmignore` file, but there is a `.gitignore` file, then npm will ignore the stuff matched by the `.gitignore` file
  - [source](https://npm.github.io/publishing-pkgs-docs/publishing/the-npmignore-file.html)
- `.stylelintrc.js`
  - Stylelint config file
- `index.html`
- `package-lock.json`
  - a file that is automatically generated for any operations where npm modifies either the `node_modules` tree, or `package.json`
- `package.json`
  - the heart of any Node project
  - it records important metadata about a project which is required before publishing to NPM
  - it defines functional attributes of a project that npm uses to install dependencies, run scripts, and identify the entry point to our package
- `postcss.config.js`
  - postcss config file
- `README.md`
- `shopify.config.js`
  - shopify config file
- `tailwind.config.js`
  - tailwind config file
- `vite.config.js`
  - vite config file

## TypeScript

This boilerplate project does NOT come with a TypeScript variant. We must manually convert all files to TypeScript.

## Organization

- Client and server based components CAN be moved into a corresponding folder
  - Meaning, in `src/components`, you create 2 folders: `client` and `server`
  - Move each component into their respective folder
  - Just have to update all the import paths (typically done automatically with TypeScript, will have to be done manually with JavaScript)

## Tailwind

- Steps to remove Tailwind can be found in the docs [here](https://shopify.dev/custom-storefronts/hydrogen/framework/css-support#remove-tailwind)

## Glossary

- Hydration
  - When talking about hydration with React specifically, this is referring to `ReactDOM.hydrate()`
  - ReactDOM.hydrate() is used for when the server renders the whole HTML of a specific page
  - The HTML is sent to the client, but the page is not responsive
  - It becomes responsive only after the accompanying functionality is downloaded from the server and the ReactDOM.hydrate() method is called on the load event of the scripts and hooks up the functionality with the rendered markup
- Middleware
  - Middleware (in regards to React) allows for side effects to be run without blocking state updates
- React Server Components (RSC)
  - RSCs allow developers to build apps that span both the server and client, combining the rich interactivity of client-side apps with the improved performance of traditional server rendering
  - Great video explaining them [here](https://www.youtube.com/watch?v=DuSa5E-GgwU)
  - Shopify documentation [here](https://shopify.dev/custom-storefronts/hydrogen/framework/react-server-components) as well

## Updating

### ESLint

### Styled Components
