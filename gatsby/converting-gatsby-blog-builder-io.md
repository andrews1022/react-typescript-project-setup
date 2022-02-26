# Gatsby Starter - Converting Gatsby Starter Blog to Builder.io

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
- Download the starter: `npx gatsby new . https://github.com/gatsbyjs/gatsby-starter-blog`
- After that is done, open it in VS Code: `code .`
- Remove git from this project (to start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page to commit this progress:

```
git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/GITHUB_USERNAME/PROJECT_NAME.git && git push -u origin main
```

## Starter Cleanup

- package.json scripts
  - alphabetized scripts in package.json
  - updated develop script to run clean first
  - also renamed to dev for short
  - also removed start script as it was redundant
- removed `.prettierrc`
- ran npm run dev to make sure projec starts up fine
- quickly formatted every file in src

## Adding Builer.io

- Download the following packages:
  - `@builder.io/gatsby`
  - `dotenv`
  - `path`
    - Run the command: `npm i @builder.io/gatsby dotenv path`
- Create .env file at roov level
  - Added this: `BUILDER_IO_API_KEY=[PUBLIC_API_KEY]`
- Open gatsby-config.js

  - Add this at the top:

  ```
  const path = require('path');

  require('dotenv').config({
    path: `.env.${process.env.NODE_ENV}`
  });
  ```

- Add this to `plugins` array:

```
{
  resolve: '@builder.io/gatsby',
  options: {
    publicAPIKey: process.env.BUILDER_IO_API_KEY,
    templates: {
      page: path.resolve('templates/my-page.tsx')
    }
  }
}
```

- 2 errors with plugins object:
  - process.env didn't work
  - path to page file incorrect
- fixed plugins object:

```
{
  resolve: '@builder.io/gatsby',
  options: {
    publicAPIKey: '1cdfe091adff409ca8fd6ad156c0d2bb',
    templates: {
      page: path.resolve('src/pages/page.js')
    }
  }
}
```

- stopped dev server if open, then run `npm run dev`
- go builder admin, and create a couple of pages
  - Home, path of '/'
  - About Us, path of '/about-is
- once dev server started up:

  - home page was still gatsby blog default
  - about-us didn't work, saw this error:

  ```
  Error in function Layout in ./src/components/layout.js:6

  Cannot read properties of undefined (reading 'pathname')
  ```

- went to layout.js and removed all code relating to `isRootPath` as it was the cause of location.pathname
- created new component: `RenderLink`

```
import React from 'react';
import { Link } from 'gatsby';

const RenderLink = (props) => {
	const internal = props.target !== '_blank' && /^\/(?!\/)/.test(props.href);

	if (internal) {
		return <Link activeClassName='active-link' to={props.href} {...props} />;
	}

	return <a {...props} />;
};

export default RenderLink;
```

- back in layout.js
  - added: `import { BuilderComponent } from '@builder.io/react';`
  - package not installed, oops: `npm i @builder.io/react`
- saw that queries could not be in run in non-page files, so moved to page.js template
  - added this graphql query:
  ```
  export const pageQuery = graphql`
  	query ($path: String!) {
  		allBuilderModels {
  			page(target: { urlPath: $path }, limit: 1, options: { cachebust: true }) {
  				content
  			}
  		}
  	}
  `;
  ```
- updated functional component to:

```
const Page = ({ data }) => {
	const [page] = data.allBuilderModels.page;
	const { content } = page;

	return (
		<Layout>
			<p>Page Template for Builder.io!!</p>
			{/* this is where each block is rendered on a given page from the builder.io ui */}
			<BuilderComponent content={content} name='page' renderLink={RenderLink} />
		</Layout>
	);
};

export default Page;
```

- deleted index.js from pages folder, shut down server, and restarted: `npm run dev`
  - home page is now using content from builder.io
- updating builder.io plugin to use env variable

  - updated imports:

  ```
  const path = require('path');
  const dotenv = require('dotenv');

  dotenv.config();
  ```

  - updated object:

  ```
  {
    resolve: '@builder.io/gatsby',
    options: {
      publicAPIKey: process.env.BUILDER_IO_API_KEY,
      templates: {
        page: path.resolve('src/pages/page.js')
      }
    }
  }
  ```

## Converting to TypeScript

- change components and pages to typescript
- update gatsby api files to typescript

## To Do

- Remove gatsby-browser.js (when converting to TS)
- Remove the following packages:
  - Pckg
