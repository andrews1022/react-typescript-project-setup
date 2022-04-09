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

## Updating

### package.json

- Group all `dependencies` together alphabetically then remove `devDependencies`:

```
"@headlessui/react": "^1.5.0",
"@shopify/hydrogen": "^0.12.0",
"@shopify/prettier-config": "^1.1.2",
"@shopify/stylelint-plugin": "^10.0.1",
"@tailwindcss/typography": "^0.5.0",
"autoprefixer": "^10.4.1",
"body-parser": "^1.19.1",
"compression": "^1.7.4",
"cross-env": "^7.0.3",
"eslint-plugin-hydrogen": "^0.6.2",
"eslint": "^7.31.0",
"graphql-tag": "^2.12.4",
"npm-run-all": "^4.1.5",
"path-to-regexp": "^6.2.0",
"postcss": "^8.4.5",
"prettier": "^2.3.2",
"react-dom": "0.0.0-experimental-529dc3ce8-20220124",
"react": "0.0.0-experimental-529dc3ce8-20220124",
"serve-static": "^1.14.1",
"stylelint": "^13.13.0",
"tailwindcss": "^3.0.0",
"vite": "^2.8.0"
```

- Order scripts alphabetically:

```
"build:client": "vite build --outDir dist/client --manifest",
"build:server": "vite build --outDir dist/server --ssr @shopify/hydrogen/platforms/node",
"build:worker": "cross-env WORKER=true vite build --outDir dist/worker --ssr @shopify/hydrogen/platforms/worker-event",
"build": "yarn build:client && yarn build:server && yarn build:worker",
"dev": "vite",
"lint:css": "stylelint ./src/**/*.{css,sass,scss}",
"lint:js": "eslint --no-error-on-unmatched-pattern --ext .js,.ts,.jsx,.tsx src",
"lint": "npm-run-all lint:*",
"preview": "npx @shopify/hydrogen-cli@latest preview",
"serve": "node --enable-source-maps dist/server"
```

### Prettier & ESLint

To use your own Prettier settings:

- Remove `"prettier": "@shopify/prettier-config",` from package.json
  - Remove from dependencies in package.json: `prettier` & `@shopify/prettier-config`
- Remove stylelint:
  - Remove from dependencies in package.json: `stylelint` & `@shopify/stylelint-plugin`
  - Delete `.stylelintrc.js` file at root level
- Remove `lint` scripts
- Delete `.eslintrc.js` at root level
- Remove `eslint` & `eslint-plugin-hydrogen` from dependencies

### Organizing Components

- created 2 folders in components: `client` and `server`
- moved components into the corresponding folder
- convert all functions to use function expression syntax

## TypeScript

This boilerplate project does NOT come with a TypeScript variant. We must manually convert all files to TypeScript.

- Install the following packages:
  - `typescript`
  - `@types/node`
  - `@types/react`
  - `@types/react-dom`
- Run the command: `npm i typescript @types/node @types/react @types/react-dom`

- Install the following types packages:
  - `@types/body-parser`
  - `@types/compression`
  - `@types/serve-static`
  - `@types/tailwindcss`
- Run the command: `npm i @types/body-parser @types/compression @types/serve-static @types/tailwindcss`

- Add `tsconfig.json` file: `tsc --init`
- Copy and paste this code in:

```
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
    "target": "es5"
  },
  "include": ["./src"],
  "exclude": ["./node_modules"]
}
```

## ESLint Setup

- Start by running: `npx eslint --init`
- This is how I answer the questions:
  - How would you like to use ESLint? · `style`
  - What type of modules does your project use? · `esm`
  - Which framework does your project use? · `react`
  - Does your project use TypeScript? · No / `Yes`
  - Where does your code run? · `browser`, `node`
  - How would you like to define a style for your project? · `guide`
  - Which style guide do you want to follow? · `airbnb`
  - What format do you want your config file to be in? · `JSON`
- Download any additional packages if prompted
- Copy and paste in rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)
- Commit this progress: `git add . && git commit -m 'added eslint' && git push -u origin main`

## Replacing Tailwind w/ Styled Components

- Steps to remove Tailwind can be found in the docs [here](https://shopify.dev/custom-storefronts/hydrogen/framework/css-support#remove-tailwind)
