# Hydrogen Notes

These are just a collection of my notes from digging around with [Hydrogen](https://github.com/Shopify/hydrogen).

## Links

- [GitHub Page](https://github.com/Shopify/hydrogen)
- [Documentation](https://shopify.dev/custom-storefronts/hydrogen)

## Setup

- Run the command: `npx create-hydrogen-app`
  - Give it a name to create a folder
  - Cannot do: `npx create-hydrogen-app .` to create in an existing directory. Must create the directory
- After run `cd [PROJECT_NAME] && code . && npm i --legacy-peer-deps && npm run dev`

## Project Contents

List of files/folders and their purpose:

- `.devcontainer`
  - a `devcontainer.json` file in your project tells VS Code how to access (or create) a development container with a well-defined tool and runtime stack
  - this container can be used to run an application or to separate tools, libraries, or runtimes needed for working with a codebase.
  - [source](https://code.visualstudio.com/docs/remote/containers)
- `.vscode`
- `node_modules`
- `public`
- `src`
- `.eslintrc.js`
- `.gitignore`
- `.npmignore`
  - used to keep stuff out of your package
  - if there's no `.npmignore` file, but there is a `.gitignore` file, then npm will ignore the stuff matched by the `.gitignore` file
  - [source](https://npm.github.io/publishing-pkgs-docs/publishing/the-npmignore-file.html)
- `.stylelintrc.js`
- `index.html`
- `package-lock.json`
- `package.json`
- `postcss.config.js`
- `README.md`
- `shopify.config.js`
- `tailwind.config.js`
- `vite.config.js`
