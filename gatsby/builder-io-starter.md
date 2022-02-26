# Gatsby Starter - Builder.io

This is my personal approach for generating a project using the [Builder.io Gatsby Starter](https://github.com/BuilderIO/gatsby-starter-builder) and update it to:

- Use TypeScript
- Use Styled Components
- Deploy to Netlify
- Continuous Deployment with Builder.io and Netlify

## Builder.io Setup

- Create a free account with Builder.io [here](https://builder.io/signup) first if you don't already have one
- Once in your dashboard, click "Create a new Builder space" and give it a name
- Optional: Click this [link](https://builder.io/fork-sample-org) to generate some basic starter content for your space

## GitHub Setup

- Create a new repo on GitHub [here](https://github.com/new)
  - Give it at least a name, then click `Create repository`
  - Keep this tab open
- On your local machine, create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
  - Where `PROJECT_NAME` matches the name of the GitHub repo you just created
- Download the starter: `npx gatsby new . https://github.com/BuilderIO/gatsby-starter-builder`
- After that is done, open it in VS Code: `code .`
- Remove git from this project (to start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page to commit this progress:

```
git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/GITHUB_USERNAME/PROJECT_NAME.git && git push -u origin main
```

## Update API Keys

- In Builder.io, click on the Account icon on the left sidenav
- Change the Site URL to `http://localhost:8000` and copy the Public API Key.
- In your code editor, add the Public API Key you just copied to `src/config.js`:

```
builderAPIKey: '59bb518773c14842921abe05d5e2bee3' <-- replace this with your API Key
```

- Commit this progress: `git add . && git commit -m 'updated api key' && git push -u origin main`

## Netlify

### Initial Setup

- Download & install the following packages: `npm i netlify-cli`
- Authenticate and obtain an access token by running: `npx netlify login`
- Init a new Netlify site: `npx netlify init`
  - Select `Create & configure a new site`
  - Select your team
  - Give the site a name (or leave blank, as it can always be renamed later)
  - Choose `Authorize with GitHub through app.netlify.com`
    - Click Authorize where needed
    - Any other time this step will be done automatically and the CLI will go directly to the next step
  - Set `Your build command` to: `npm run build`
  - Set `Directory to deploy` to: `public`
  - Leave Netlify functions blank (just hit `Enter`)
  - Netlify should detect you are using Gatsby, so type `y` to install the `Essential Gatsby plugin`
  - Select `n` to not add the `netlify.toml` file
- Go to the Netlify Dashboard [here](https://app.netlify.com/)
  - Go to Sites and make sure the site was successfully deployed
  - The build can take a few minutes, so you will have to wait!
- Commit this progress: `git add . && git commit -m 'setup netlify deployment' && git push -u origin main`

### Continuous Deployment

For continuous deployment between Netlify & Builder.io, we need to do 2 things:

- Create a [build hook](https://docs.netlify.com/configure-builds/build-hooks/) in Netlify
- Add the build hook from last step to Builder.io global webhooks in your new [space settings](https://builder.io/account/space)

Netlify Build Hook:

- Go to the Netlify Dashboard [here](https://app.netlify.com/)
- Go to Sites and select your site
- Go to `Site settings` > `Build & deploy` > `Continuous deployment`, and then scroll down the `Build hooks` section
- Click `Add build hook`
  - Give it a name, then click `Save`
  - Copy the generated URL

Builder.io Space Settings:

- Go to your Builder.io space settings [here](https://builder.io/account/space)
- Click the pencil icon for Global `webhooks`
- Click `+ WEBHOOK`
- Click on `Webhook 1`
- Paste in the URL
- Click `Save`

Testing the Hook:

- Go to your Builder.io content [here](https://builder.io/content)
- Click on `Landing page`, then `Home`
- Make a simple change to the heading _"We make things great"_, for example
- Click `PUBLISH UPDATE`
- Go to the Netlify Dashboard [here](https://app.netlify.com/)
  - Go to Sites and you should see the site is rebuilding!

## Creating a Custom Code Component

Follow these steps to create a `Code Component`. A `Code Component` is a special type of component for Builder.io, which will allow you, or whomever, is editing the site in the Builder.io UI, to drag and drop into the layout and create dyanmic content for the site. In this example, we'll create a simple HeadingAndCopy component.

### Static

- In src/components, create a new folder: `mkdir HeadingAndCopy`
- In this folder, create 2 files: `touch HeadingAndCopy.jsx HeadingAndCopy.builder.js`
  - `HeadingAndCopy.jsx` is where the typical JSX/React component code will go
  - `HeadingAndCopy.builder.js` is where the Builder.io config will go
- In `HeadingAndCopy.jsx`, create a simple component to start:

```
import React from 'react';

const HeadingAndCopy = () => {
  return (
    <div>
      <h2>Heading here</h2>
      <p>
        Lorem, ipsum dolor sit amet consectetur adipisicing elit. Repellat,
        eius.
      </p>
    </div>
  );
};

export default HeadingAndCopy;
```

- In `HeadingAndCopy.builder.js`, add this starter code:

```
import { Builder } from '@builder.io/react';
import HeadingAndCopy from './HeadingAndCopy';

Builder.registerComponent(HeadingAndCopy, {
  name: 'Heading and Copy'
});
```

- Finally, go to `src/builder-settings.js` and import the builder file: `import './components/HeadingAndCopy/HeadingAndCopy.builder';`
- Now when you go to edit a page in Builder.io, you should see this component underneath "Code Components"
  - Drag and drop it onto the page

### Dynamic

Now let's update this HeadingAndCopy component to use dynamic values in the editor

- In `HeadingAndCopy.builder.js`, we can add `inputs` to our Code component:

```
Builder.registerComponent(HeadingAndCopy, {
  name: 'Heading and Copy',
  inputs: [
    {
      name: 'title',
      type: 'string'
    },
    {
      name: 'copy',
      type: 'string'
    }
  ]
});
```

- These inputs will allow the user to dynamically render the same component, but with different values

- In `HeadingAndCopy.jsx`, let's update the JSX to use these dynamic values
  - To access the values from these inputs, we take the `name` key from each object in the inputs array and access them via props
  - So, with this component it will be `props.copy` & `props.title`
  - We can use destructuring to clean up the code a bit, and have it look like this:
  ```
  const HeadingAndCopy = ({ copy, title }) => {
    return (
      <div>
        <h2>{title}</h2>
        <p>{copy}</p>
      </div>
    );
  };
  ```
- Now, this component is 100% dynamic!
- This is a simple example, but your component can truly be **ANYTHING** you can think of!

### See the Changes

To see the changes after clicking `Publish Update` in Builder.io:

- Locally:
  - Simply stop the local dev server, then run the command `npm run dev`
  - This will clear the `.cache` and `public` folders, and start up the local dev server agaub, which gives you the lastest changes from Builder.io
- Netlify:

  - Simply commit the changes
  - When you click `Publish Update`, this _will_ trigger a rebuild on Netlify because of our webhook, but the component will not show up until you commit the changes to GitHub

## Layout Files Explained:

- PageLayout.jsx
  - for an example on wrapping your pages with conent from header and footer model entries.
- RootLayout.jsx
  - for rendering react-helmet and helmet related data, along with material-ui theme
- LandingPage.jsx
  - for using GraphQL to query and render Builder.io components and pages manually in parts of your Gatsby site and content
- Hierarchy (HTML Simplified):
  - body
    - div # \_\_\_gastby
      - RootLayout
      - d # gatsby-focus-wrapper
        - div . makeStyles-root-1
          - div . makeStyles-header-2
          - PageLayout
          - div . makeStyles-content-4
            - LandingPage
            - div. builder-component
              - Deeply nested within (5 levels down), each block is rendered here
            - LandingPage
          - PageLayout
          - div . makeStyles-footer-3
      - RootLayout
- Hierarchy (JSX Simplified):
  - RootLayout
    - PageLayout
      - LandingPage

## Uninstalling Some NPM Packages

- Uninstalled these packages:
  - gh-pages
  - prettier
    - Run the command: `npm un gh-pages prettier`

## Organizing Components Into Folders

Builder starter already had components in their own folders, which is nice!

## Components & Pages

Making sure all components/pages follow this structure:

- Components use function expression syntax
- graphql query is placed BELOW export default of component
- Destructure props where possible

## Converting to TypeScript

- Looks like Gatsby Version is far out of date, possiblly need to different starter and add in Builder.io instead of using Builder.io starter ?

## Adding ESLint

- Also guessing since things are out-of-date, ESLint can't properly be added either
- Kept getting rules were not found?

## Add New "Navigation" Model

- Tried creating new `Navigation` model
  - The idea was that this model would simply allow you to add pages, and you get the data back to create links in the JSX
- While you can create a data model that returns a list of references to Landing pages, you don't get enough data to use it for anything
- It return some ids, and other useless data
- This query works just fine:

```
query MyQuery {
  allBuilderModels {
    landingPage {
      id
      name
      data {
        title
        url
      }
    }
  }
}
```

## Removing Pre-Existing Models

- Footer
  - Builder.io admin
    - Models, Footer
    - 3 dots, Delete
  - Code
    - PageLayout.tsx, remove from graphql query and removed const variable
- Header
  - Same as steps taken for Footer
  - Extra steps taken:
    - Return JSX inside of StaticQuery instead, since we are not querying anything anymore

## Starter Cleanup

- Update README
- Update package.json scripts (shorten develop to dev)
- Uninstall material-ui packages
- Remove unnecessary files & folders
- Update builder-io plugin in gatsby-config to use environment variable instead of importing api key
