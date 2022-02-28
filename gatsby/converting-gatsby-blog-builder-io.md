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
- ran `npm run dev` to make sure project starts up fine
- quickly formatted every file in `src`

## Adding Builer.io

- Download the following packages:
  - `@builder.io/gatsby` --> used as a plugin in gatsby-config
  - `@builder.io/react` --> used for <BuilderComponent /> later
  - `dotenv`--> used in gatsby-config
  - `path`--> used in gatsby-config
    - Run the command: `npm i @builder.io/gatsby @builder.io/react dotenv path`
- Create .env file at roov level
  - Add this to it: `BUILDER_IO_API_KEY=[PUBLIC_API_KEY]`
- Open `gatsby-config.js`

  - Add this at the top:

  ```javascript
  const path = require('path');

  require('dotenv').config({
  	path: `.env.${process.env.NODE_ENV}`
  });
  ```

- Add this to `plugins` array:

```javascript
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

```javascript
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
  - Home, path of `/`
  - About Us, path of `/about-us`
- once dev server started up:

  - home page was still gatsby blog default
  - about-us didn't work, saw this error:

  ```
  Error in function Layout in ./src/components/layout.js:6

  Cannot read properties of undefined (reading 'pathname')
  ```

- went to layout.js and removed all code relating to `isRootPath` as it was the cause of location.pathname
- created new component: `RenderLink`

```javascript
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
- saw that queries could not be in run in non-page files, so moved this query `page.js` template
  ```javascript
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
- updated `page.js` to:

```javascript
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

- deleted `index.js` from pages folder, shut down server, and restarted: `npm run dev`
  - home page is now using content from builder.io
- updating builder.io plugin to use env variable

  - updated imports:

  ```javascript
  const path = require('path');
  const dotenv = require('dotenv');

  dotenv.config();
  ```

  - updated object:

  ```javascript
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

## Adding Simple Blog Post

- follwed this [part of docs](https://www.builder.io/c/docs/creating-a-blog)

- go to [models](builder.io/models) in builder.io dashboard
- click `+create model`, choose `section`
- name it `Authors`
- add the following fields:
  - full name, type `text`
  - bio, type `long text`
  - picture, type `file`
- click save

- create another model, name it `Blog Post`
- give it a short description
- add the following fields:

  - title, type `text`
  - handle (aka slug), type `text`
  - image, type `file`
  - date, type `timestamp`
  - blurb, type `long text`
  - author, type `reference`, choose to reference Author

- go to [content](builder.io/content)
- select `author`
- click `+ new`
- create a new author, then click `publish`

- back in content, select `blog post`
- click `+ new`
- fill in the fields, then click `publish`
- do it one more time so we have at least 2

- in vs code

  - gatsbt-node.js
    - replace graphql query with this:
    ```javascript
    const result = await graphql(
    	`
    		{
    			allBuilderModels {
    				blogPost {
    					data {
    						slug
    					}
    				}
    			}
    		}
    	`
    );
    ```
    - add this above const posts:
    ```javascript
    if (!result.data) {
    	throw new Error('Failed to get posts.');
    }
    ```
    - change const posts to this:
    ```javascript
    const posts = result.data.allBuilderModels.blogPost;
    ```
    - change the if (posts.length) check to:
    ```javascript
    if (posts.length > 0) {
    	posts.forEach((post) => {
    		createPage({
    			path: `/blog/${post.data.slug}`,
    			component: blogPost,
    			context: {
    				// data passed to context is available in page queries as graphql variables
    				slug: post.data.slug
    			}
    		});
    	});
    }
    ```
  - blog-post.js

    - change the pageQuery to this:

    ```javascript
    export const pageQuery = graphql`
    	query ($slug: String!) {
    		allBuilderModels {
    			blogPost(target: { urlPath: $slug }, limit: 1, options: { cachebust: true }) {
    				data {
    					title
    					blurb
    				}
    			}
    		}
    	}
    `;
    ```

    - change BlogPostTemplate to this:

    ```javascript
    const BlogPostTemplate = ({ data, location }) => {
    	const [blogPost] = data.allBuilderModels.blogPost;
    	const { blurb, title } = blogPost.data;

    	return (
    		<Layout location={location}>
    			<Seo title={title} />

    			<article>
    				<h1>{title}</h1>
    				<p>{blurb}</p>
    			</article>

    			<Bio />
    		</Layout>
    	);
    };
    ```

  - back to `gatsbt-node.js`
    - can comment out/remove both `onCreateNode` & `createSchemaCustomization` functions
    - can also comment out/remove this import at the top:
    ```javascript
    const { createFilePath } = require(`gatsby-source-filesystem`);
    ```

- created folders for each component
  - moved components into their corresponding folders
  - also renamed to CamelCase
  - didn't bother renaming to index.js yet
- created simple code component

  - new folder in components, HeadingAndCopy
  - create HeadingAndCopy.js
  - simple code snippet:

  ```javascript
  import React from 'react';

  const HeadingAndCopy = () => {
  	return (
  		<div>
  			<h2>Heading here</h2>
  			<p>Lorem ipsum dolor sit amet.</p>
  		</div>
  	);
  };

  export default HeadingAndCopy;
  ```

  - create another file: `HeadingAndCopy.builder.js`
  - import Builder and the component:

  ```javascript
  import { Builder } from '@builder.io/react';
  import HeadingAndCopy from './HeadingAndCopy';
  ```

  - call registerComponent method like so:

  ```javascript
  Builder.registerComponent(HeadingAndCopy, {
  	name: 'Heading and Copy'
  });
  ```

- go to [builder content](https://builder.io/content)
  - click on Page, then Home Page
  - was not showing up
- back in vs code

  - created `builder-settings.js` in src folder
  - added this code:

  ```javascript
  import { builder } from '@builder.io/react';

  // a set of widgets you can use in the editor, optional.
  import '@builder.io/widgets';

  // Import all custom components so you can use in the builder.io editor here
  import './components/HeadingAndCopy/HeadingAndCopy.builder';

  builder.init('1cdfe091adff409ca8fd6ad156c0d2bb');
  ```

  - this was to match the builder.io config, but still the component didn't show up

- tried moving code gatsby-config

## Converting to TypeScript

- change components and pages to typescript
- update gatsby api files to typescript

## To Do

- Remove gatsby-browser.js (when converting to TS)
- Remove the following packages:
  - Pckg
