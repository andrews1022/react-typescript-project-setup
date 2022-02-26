# Gatsby Starter - Contentful Blog

This is my personal approach for generating a project using the [Contentful Blog Gatsby Starter](https://github.com/contentful/starter-gatsby-blog) and update it to:

- Use TypeScript
- Use Styled Components
- Host on Gatsby Cloud
- Continuous Integration with GitHub
- Automatic re-builds on Gatsby Cloud

## Contentful Setup Pt. 1

- Create a free account with Contentful [here](https://www.contentful.com/sign-up/) first if you don't already have one
  - Recommend signing up with GitHub
- Login to Contentful [here](https://be.contentful.com/login)
- For any subsequent visits, you can go to your Contentful Admin [here](https://app.contentful.com/)
- Go to Settings > API keys
  - Make sure you are on the `Content delivery / preview tokens` tab
  - Click `Add API key` button in the top right corner
  - Give it a name at least, and click `Save`
  - Keep this tab open

## GitHub Setup

- Create a new repo on GitHub [here](https://github.com/new)
  - Give it at least a name, then click `Create repository`
  - Keep this tab open
- On your local machine, create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
  - Where `PROJECT_NAME` matches the name of the GitHub repo you just created
- Download the starter: `npx gatsby new . https://github.com/contentful/starter-gatsby-blog`
- After that is done, open it in VS Code: `code .`
- Remove git from this project (to start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page to commit this progress:

```
git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/GITHUB_USERNAME/PROJECT_NAME.git && git push -u origin main
```

## Contentful Setup Pt. 2

- Run the command: `npm run setup`
  - Enter in your Space ID
  - Enter in your Content Management API Access Token
    - For this, go back to Contentful in your browser
    - Go to the `Content management tokens` tab
    - Click `Generate personal token`
    - Enter a token name, then click Generate
    - Copy and paste this token
      - **NOTE**: This token will no longer be accessible from this point forward!
  - Enter in your Content Delivery API Access Token
- After that is done, now you can run `npm run dev` to start the local development server

## Gatsby Cloud Setup

- Sign up with Gatsby Cloud [here](https://www.gatsbyjs.com/products/cloud/) if you don't already have an account
- For any subsequent visits, you can go to your Gatsby Cloud Dashboard [here](https://www.gatsbyjs.com/dashboard/)

- Click `Add a site +`
- Import from a GitHub repository
  - May need to authorize Gatsby Cloud
- Find the repo, and click `Import`
- For `Basic Configuration`, leave the setting as-is and click `Next`
- For `Connect Integrations`, Gatsby Cloud should automatically detect your are using Contentful based on your config
  - Click `Connect`, then click `Authorize`, then click `Authorize` again
  - Select the Space you created earlier, then click Continue
- For `Environment variables`, you only need the following:
  - Build Variables
    - `CONTENTFUL_ACCESS_TOKEN` --> your content delivery API access token
    - `CONTENTFUL_SPACE_ID` --> your Contentful space ID
  - Preview Variables
    - `CONTENTFUL_PREVIEW_ACCESS_TOKEN` --> your content preview API access token
    - `CONTENTFUL_HOST` --> `preview.contentful.com`
    - `CONTENTFUL_SPACE_ID` --> your Contentful space ID
  - All others not listed above can be removed
  - Click `Save`
- Click `Build site`
  - The build can take a few minutes, so you will have to wait!

## Testing the Workflow

### Simple Code Change

- In VS Code, go to `/src/components/article-preview.js`
- Find this piece of JSX:

```javascript
<h2 className={styles.title}>{post.title}</h2>
```

- Simply add on a few exclamation points after `{post.title}`:

```javascript
<h2 className={styles.title}>{post.title}!!</h2>
```

- Commit the change: `git add . && git commit -m 'quick commit for testing workflow' && git push -u origin main`
- Go to your Gatsby Dashboard
  - This should've triggered a re-build of the site
  - You might need to just refresh the page

### Simple Contentful Change

- Go to the Contentful Space
  - Go to the `Content` tab, and click on the `John Doe` piece of content
  - Make a simple change, like to the name, then click `Publish Changes`
- Go to your Gatsby Dashboard
  - The build time for this is typically VERY quick, ~3-4 seconds
  - You should see the change on the site
- So, at this point, we have confirmed whenever we either:
  - A) Make a change in the code and commit / push to GitHub
  - B) Make a change in the Contentful backend
- Gatsby Cloud will automatically trigger a re-build of the site, keeping it up-to-date at all times.

## Starter Cleanup

### Removing Unnecessary Files & Folders

Here is a list of the files and folders we can & cannot deleted post-setup:

✓ --> CAN be removed

✕ --> CANNOT be removed

- [✓] `.cache` --> can be deleted, but is regenerated each time you rebuild, and is ignored by git anyways
- [✓] `/bin` & related `package.json` scripts --> used for running `npm run dev` to setup contentful
- [✓] `/contentful` --> used for running `npm run dev` to setup contentful
- [✓] `/node_modules` --> can be deleted, but is regenerated each time you install packages, and is ignored by git anyways
- [✓] `/public` --> can be deleted, but is regenerated each time you rebuild, and is ignored by git anyways
- [✕] `/src` --> essential
- [✕] `/static` --> used to house files like robots.txt and favicon
- [✓] `_config.yml` --> used for GitHub pages
- [✕] `.babelrc` --> babel config file
- [✓] `.contentful.json.sample` --> sample contentful data file
- [✕] `.gitignore` --> used to intentionally ignore/not track specific files/folders
- [✕] `.npmrc` --> configuration file for NPM, defines the settings on how NPM should behave when running commands
- [✕] `.nvmrc` --> specify which node version the project should use
- [✓] `.prettierrc` --> config for prettier, this is entirely subjective so up to you (I use Prettier settings in VS Code)
- [✓] `.travis.yml` --> config file for Travis CI (a hosted continuous integration service)
- [✓] `app.json` --> unsure, not used anywhere
- [✕] `gatsby-config.js` --> will convert to typescript later on
- [✕] `gatsby-node.js` --> will convert to typescript later on
- [✕] `LICENSE` --> ok to leave, no harm
- [✓] `package-lock.json` --> can be deleted, but is regenerated each time you install packages
- [✕] `package.json` --> essential
- [✕] `README.md` --> essential
- [✓] `screenshot.png` --> was used in the README, no longer needed
- [✓] `static.json` --> unsure, not used anywhere, possibly used for Heroku
- [✓] `WHATS-NEXT.md` --> Simple markdown file

You may remove the files / folders marked with a '✓' as you wish.

Run this command to remove most files listed above with a ✓:

`rm -rf bin contentful _config.yml .contentful.json.sample .prettierrc .travis.yml app.json package-lock.json screenshot.png static.jso WHATS-NEXT.md`

### Updating NPM Scripts

- Open `package.json`:
  - Add the `gatsby clean` script back in: `"clean": "gatsby clean"`
  - Update the `dev` command to be: `"dev": "npm run clean && gatsby develop"`
  - Add this utility script as well: `"troubleshoot": "rm -rf .cache node_modules public package-lock.json && npm i && npm run dev"`
- If you encounter a git error along the lines of `Fatal unable to access, could not resolve host` when trying to commit changes, it's likely a proxy issue. Simply run this command and it should fix the problem:
  `git config --global --unset http.proxy && git config --global --unset https.proxy`

### Components & Pages

- Let's convert all the files using classes to use the function expression syntax. The list of files we need to adjust are:

- `src/components/layout.js`
- `src/pages/blog.js`
- `src/pages/index.js`
- `src/templates/blog-post.js`

- Also make sure group each component and it's CSS module file together in a folder to keep things a big organized. The file/folder structure will now be:

```
/components
  /ArticlePreview
    - index.js
    - article-preview.module.css
  /Container
    - index.js
  /Footer
    - index.js
    - footer.module.css
  etc.

```

### layout.js

Updated code:

```javascript
const Layout = ({ children, location }) => {
	return (
		<>
			<Seo />
			<Navigation />
			<main>{children}</main>
			<Footer />
		</>
	);
};

export default Layout;
```

### blog.js

Updated code:

```javascript
const BlogIndex = ({ data, location }) => {
	const posts = data.allContentfulBlogPost.nodes;

	return (
		<Layout location={location}>
			<Seo title='Blog' />
			<Hero title='Blog' />
			<ArticlePreview posts={posts} />
		</Layout>
	);
};
```

### index.js

Updated code:

```javascript
const Home = ({ data, location }) => {
	const posts = data.allContentfulBlogPost.nodes;
	const [author] = data.allContentfulPerson.nodes;

	return (
		<Layout location={location}>
			<Hero
				image={author.heroImage.gatsbyImageData}
				title={author.name}
				content={author.shortBio.shortBio}
			/>
			<ArticlePreview posts={posts} />
		</Layout>
	);
};

export default Home;
```

### blog-post.js

Updated code:

```javascript
const BlogPostTemplate = ({ data, location }) => {
	const post = data.contentfulBlogPost;
	const previous = data.previous;
	const next = data.next;

	return (
		<Layout location={location}>
			<Seo
				title={post.title}
				description={post.description.childMarkdownRemark.excerpt}
				image={`http:${post.heroImage.resize.src}`}
			/>
			<Hero
				image={post.heroImage?.gatsbyImageData}
				title={post.title}
				content={post.description?.childMarkdownRemark?.excerpt}
			/>
			<div className={styles.container}>
				<span className={styles.meta}>
					{post.author?.name} &middot; <time dateTime={post.rawDate}>{post.publishDate}</time> –{' '}
					{post.body?.childMarkdownRemark?.timeToRead} minute read
				</span>

				<div className={styles.article}>
					<div
						className={styles.body}
						dangerouslySetInnerHTML={{
							__html: post.body?.childMarkdownRemark?.html
						}}
					/>

					<Tags tags={post.tags} />

					{(previous || next) && (
						<nav>
							<ul className={styles.articleNavigation}>
								{previous && (
									<li>
										<Link to={`/blog/${previous.slug}`} rel='prev'>
											← {previous.title}
										</Link>
									</li>
								)}
								{next && (
									<li>
										<Link to={`/blog/${next.slug}`} rel='next'>
											{next.title} →
										</Link>
									</li>
								)}
							</ul>
						</nav>
					)}
				</div>
			</div>
		</Layout>
	);
};
```

### Uninstalling Some NPM Packages

- We are no longer using any of the following packages:
- `contentful-import`
- `gh-pages`
- `lodash`
- `netlify-cli`
  - Uninstall them all by running: `npm un contentful-import gh-pages lodash netlify-cli`.
- We can also simply our `scripts` in `package.json` to:

```json
"scripts": {
  "build": "gatsby build",
  "clean": "gatsby clean",
  "dev": "gatsby develop",
  "rebuild": "rm -rf .cache public && npm run dev",
  "serve": "gatsby serve",
  "troubleshoot": "rm -rf .cache node_modules public package-lock.json && npm i && npm run dev"
}
```

### Organizing Components Into Folders

- First, go into the components folder: `cd src/components/`
  - We need to create all folders for each component:
    - ArticlePreview
    - Container
    - Footer
    - Hero
    - Layout
    - Navigation
    - Seo
    - Tags
  - Create them all at once by running: `mkdir ArticlePreview Container Footer Hero Layout Navigation Seo Tags`
- Now, one at a time, move the corresponding files into their folders
  - Hopefully VS Code automatically updates the import path(s) for you
  - If not, you will have to manually update them yourself
- After moving everything around, you should see the following warning: `warn chunk commons [mini-css-extract-plugin]`
  - This error/warning is caused by the Webpack plugin `mini-css-extract-plugin` wanting all CSS imports to be in the same order
  - This is because it confused CSS modules with plain CSS.
  - However, since we will be using Styled Components, we can ignore this warning and continue.

## Converting to TypeScript

- None of Gatsby's starters are fully good-to-go in TypeScript, so we must manually convert all `.js`/`.jsx` files to `.ts`/`.tsx`
- We also don't get proper type checking. VS Code will highlight the error, but the Gatsby CLI will not.
- We will update the following to use TypeScript:
  - Components
  - Pages
  - Gatsby API files (gatsby-config, gatsby-node, etc.)
- Here is a summary of the steps we will take:
  - Setup `tsconfig.json`
  - Convert all the components & pages to TypeScript
  - Convert the Gatsby API files to use TypeScript

### Setup

- Start by installing typescript: `npm i typescript`
- Create a tsconfig file: `tsc --init`
- Select everything in `tsconfig.json`, and replace it with this:

```json
{
	"compilerOptions": {
		"esModuleInterop": true,
		"forceConsistentCasingInFileNames": true,
		"jsx": "react",
		"module": "commonjs",
		"noEmit": true,
		"pretty": true,
		"skipLibCheck": true,
		"strict": true,
		"target": "es5"
	},
	"include": ["./src", "gatsby"],
	"exclude": ["./node_modules", "./public", "./.cache"]
}
```

Next, we need to install `concurrently` which will allow us to run multiple scripts, well, concurrently: `npm i concurrently`

Next, let's update our `scripts` in `package.json` to use `concurrently`:

```json
"dev-gatsby": "gatsby develop",
"dev-typescript": "tsc -w",
"dev": "npm run clean && concurrently \"npm:dev-gatsby\" \"npm:dev-typescript\""
```

At this point, we should run our rebuild script to start fresh: `npm run rebuild`

Now we will also see any type errors in the CLI!

### Converting Pages

Now, we can convert the `.js` files to `.tsx` files. Let's start with the Home Page.

- Rename from `index.js` to `index.tsx`
- When you do that, the TypeScript will complain about a few things:
  - The components you are importing (which we will convert to `.tsx` in a bit anyways, so no worries there)
  - The props `data` & `location` have the any type implicitly
  - The types for `allContentfulBlogPost` & `allContentfulPerson` are also unknown
  - A type error for the CSS Modules (since we are replacing them with Styled Components later on, no worries here either)
- Luckily, Gatsby has types for us, and the one we need for pages is `PageProps`

  - Import it: `import type { PageProps } from 'gatsby'`
  - Then set the type of our destructured props to it:

  ```javascript
  const Home = ({ data, location }: PageProps) => {
  	// ...
  };
  ```

- Next, we'll tackle the types for the GraphQL data

  - Outside the function body, just above, create a new type called `GraphQLResult`: `type GraphQLResult = {};`
  - In this, we need to set the types for the data being returned
  - Now would be a good time to create a `types` folder, and inside it a file called `types.ts`
    - At the top, import the following: `import type { IGatsbyImageData } from 'gatsby-plugin-image';`
      - We will use this multiple times in this file, and we would get type errors in some files if we didn't
  - In `types.ts`, add the following:

  ```javascript
  export type BlogPost = {
  	title: string,
  	slug: string,
  	publishDate: string,
  	tags: string[],
  	heroImage: {
  		gatsbyImageData: IGatsbyImageData
  	},
  	description: {
  		childMarkdownRemark: {
  			html: string
  		}
  	}
  };

  export type Person = {
  	name: string,
  	shortBio: {
  		shortBio: string
  	},
  	title: string,
  	heroImage: {
  		gatsbyImageData: IGatsbyImageData
  	}
  };
  ```

  - These will be our re-usable types throughout the project
  - Back in the `index.tsx`, adjust the `GraphQLResult` type to:

  ```javascript
  type GraphQLResult = {
  	allContentfulBlogPost: {
  		nodes: BlogPost[]
  	},
  	allContentfulPerson: {
  		nodes: Person[]
  	}
  };
  ```

  - Now, we can pass this type in as an additional argument to PageProps:

  ```javascript
  const Home = ({ data, location }: PageProps<GraphQLResult>) => {
  	// ...
  };
  ```

  - And now the type errors for the Contentful data should be gone!

- Repeat this process for `blog.js`
- For the `blog-post.js` template, however:

  - After renaming to `.tsx`, you will get error saying `Module not found`
  - This is because in `gatsby-node.js`, there is this line: `const blogPost = path.resolve('./src/templates/blog-post.js');`
    - Simply change it to `.tsx` at the end
    - Ctrl + C to stop the local dev server (if not already stopped)
    - Start fresh with `npm run rebuild`
  - In `types.ts`, add the following:

  ```javascript
  export type SingleBlogPost = {
  	author: {
  		name: string
  	},
  	body: {
  		childMarkdownRemark: {
  			html: string,
  			timeToRead: number
  		}
  	},
  	description: {
  		childMarkdownRemark: {
  			excerpt: string
  		}
  	},
  	heroImage: {
  		gatsbyImageData: IGatsbyImageData,
  		resize: {
  			src: string
  		}
  	},
  	publishDate: string,
  	rawDate: string,
  	slug: string,
  	tags: string[],
  	title: string
  };

  export type NextPrevious = { slug: string, title: string } | null;
  ```

  - Back in the `blog-post.tsx`, adjust the `GraphQLResult` type to:

  ```javascript
  type GraphQLResult = {
  	contentfulBlogPost: SingleBlogPost,
  	next: NextPrevious,
  	previous: NextPrevious
  };
  ```

  - Then pass it to `PageProps`:

  ```javascript
  const BlogPostTemplate = ({ data, location }: PageProps<GraphQLResult>) => {
  	// ...
  };
  ```

### Converting Components

Now let's update the components to `.tsx`! The steps are similar to the pages:

- Rename `index.js` to `index.tsx`
- Setup type for the props (if any)

  - Example:

  ```javascript
  // props
  type ArticlePreviewProps = {
  	posts: BlogPost[]
  };

  const ArticlePreview = ({ posts }: ArticlePreviewProps) => {
  	// ...
  };
  ```

- Fix any other typing errors accordingly
- If you are having trouble/unsure how to type the pre-existing components, you can see how I did so [here](https://github.com/andrews1022/gatsby-contentful-blog/tree/main/src/components).

### Converting Gatsby API Files

Now we will convert the Gatsby API files (gatsby-config, gatsby-node, etc.) to use TypeScript. The advantage of this is if the project should grow, it will be nice to have everything type-checked.

The issue is, though, these files MUST be in .js for the Gatsby Runner to use them. So, how do we solve this problem?

To start:

- At the root level of the project, create a folder called `gatsby`
- Copy and paste `gatsby-config.js` & `gatsby-node.js` into this folder and rename them to `.ts`
- Download & install the following packages: `npm i dotenv gatsby-plugin-typescript ts-node`
  - `dotenv` because we will get an ESLint error `import/no-extraneous-dependencies`
  - `gatsby-plugin-typescript` allows Gatsby to build TypeScript and TSX files
  - `ts-node `will allow us to us to recognize the TS syntax called from the JS files
- Go to `gatsby-config.js` at the root level

  - Select everything and replace it with this:

  ```javascript
  require('ts-node').register();

  module.exports = require('./gatsby/gatsby-config');
  ```

  - Note, this `gatsby-config.js` **MUST** remain as `.js`. We will be able to switch `gatsby-node` to `.ts`, though

- Go `gatsby-config.ts` in the `gatsby` folder

  - Replace this code:

  ```javascript
  require('dotenv').config({
  	path: `.env.${process.env.NODE_ENV}`
  });
  ```

  - With this code:

  ```javascript
  import dotenv from 'dotenv';

  dotenv.config({ path: `.env.${process.env.NODE_ENV}` });
  ```

  - Also update the object with the plugins, etc., being exported at the bottom from this:

  ```javascript
  module.exports = {
  	// ...
  };
  ```

  - To this:

  ```javascript
  export default {
  	// ...
  };
  ```

  - Lastly, we need to update the `contentfulConfig` object to include this: `host: process.env.CONTENTFUL_HOST`
  - If we don't we get an error down below in the if check because we try to access `contentfulConfig.host`, but host doesn't exist initially in this variable
  - It should look like this:

  ```javascript
  const contentfulConfig = {
  	accessToken: process.env.CONTENTFUL_ACCESS_TOKEN || process.env.CONTENTFUL_DELIVERY_TOKEN,
  	host: process.env.CONTENTFUL_HOST,
  	spaceId: process.env.CONTENTFUL_SPACE_ID
  };
  ```

Now to update gatsby-node:

- For the `gatsby-node.js` file at the `root` level, we can actually rename it to `.ts`
- Once you do, select everything and replace it with just this: `export * from "./gatsby/gatsby-node";`
  - You will get an error saying that this file is not a module
  - We just need to update the file to use the import/export syntax
- Open `gatsby-node.ts` in the `gatsby` folder

  - Replace this: `const path = require('path');`, with this: `import { resolve } from 'path';`
  - Import the following type from the `gatsby` package: `import type { GatsbyNode } from 'gatsby';`
  - Next, update the function from this:

  ```javascript
  exports.createPages = async ({ graphql, actions, reporter }) => {
  	// ...
  };
  ```

  - To this:

  ```javascript
  export const createPages: GatsbyNode['createPages'] = async ({ graphql, actions, reporter }) => {
  	// ...
  };
  ```

- At this point, we should see a type error down below for `const posts = result.data.allContentfulBlogPost.nodes` saying:
  `Property 'allContentfulBlogPost' does not exist on type 'unknown'`
- We need to setup the type for the result from the GraphQL query
- Just outside & above createPages function, create a type called `GraphQLResult`

  - The type should look like this:

  ```javascript
  type GraphQLResult = {
  	allContentfulBlogPost: {
  		nodes: {
  			slug: string,
  			title: string
  		}[]
  	}
  };
  ```

- Next, simply apply this type to the GraphQL result variable and the error should go away:

```javascript
const result = await graphql<GraphQLResult>(
  // ...
);
```

- And now another error should appear on result.data saying `Object is possibly 'undefined'`
- Just above this line, add the following if check and the error should go away:

```javascript
if (!result.data) {
	throw new Error('Failed to get posts.');
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

## Styled Components Setup

- Download & install the following packages: `npm i babel-plugin-styled-components gatsby-plugin-styled-components react-is styled-components @types/styled-components`
  - `react-is` because if we don't, we get an error on Gatsby Cloud saying: `Can't resolve 'react-is' ...`
  - `babel-plugin-styled-components`, `gatsby-plugin-styled-components`, & `styled-components` are the packages recommended by Gatsby themselves
  - `@types/styled-components` is needed since styled-components don't come with types out of the box
- Open `gatsby-config.js`:
  - In the `plugins` array, add this: `'gatsby-plugin-styled-components',`
  - Start fresh with: `npm run rebuild`

### Simple Component Change

- Let's make a simple adjustment to `ArticlePreview`

  - In the ArticlePreview folder, create a file called: `styles.ts`
  - Import styled-components: `import styled from 'styled-components';`
  - Open up the CSS modules file
  - Let's convert .article-list to a styled component
  - Copy and paste this into styles.ts:

  ```javascript
  export const ArticleList = styled.ul`
  	display: grid;
  	grid-gap: 48px;
  	grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  	list-style: none;
  	margin: 0;
  	padding: 0;
  `;
  ```

- Back in index.tsx, add the following:

  ```javascript
  // styled components
  import * as S from './styles';
  ```

  - We use `import * as S` so we can differentiate styled components from our functional components
  - In the JSX, replace this:

  ```javascript
  <ul className={styles.articleList}>// ...</ul>
  ```

  - With this:

  ```javascript
  <S.ArticleList>// ...</S.ArticleList>
  ```

  - And if we check the Elements Tab in DevTools, we should see something like:

  ```javascript
  <ul class='styles__ArticleList-bfmZnV jUEOQo'>// ...</ul>
  ```

  - Of course, the randomly generated class names will be different than from what you see here

### Adding Global Styles

Now, let's add some global styles to the project. For that we will need 2 things:

- GlobalStyle component
- Theme object

First, let's create the GlobalStyle component:

- Inside the src folder, create a new folder called `styles`
- In this folder, create a file called: `GlobalStyle.ts`
- In this file, import createGlobalStyle: `import { createGlobalStyle } from "styled-components";`
- Next add this starting code:

```javascript
const GlobalStyle = createGlobalStyle``;

export default GlobalStyle;
```

- Inside the backticks is where you can place your global styles you want applied. Let's copy and paste some from global.css into there and make the necessary adjustments:

```javascript
const GlobalStyle = createGlobalStyle`
  html {
    scroll-behavior: smooth;
  }

  html * {
    box-sizing: border-box;
  }

  body {
    background: #fff;
    color: #000;
    font-family: 'Inter var', -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';
    font-size: 16px;
    font-weight: 400;
    line-height: 1.5;
    margin: 0;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
  }
`;
```

Next, let's create the global theme object

- Inside the styles folder, create a new file called theme.ts, and added this code to start:

```javascript
const theme = {
	mediaQueries: {
		desktopHD: 'only screen and (max-width: 1920px)',
		desktopMedium: 'only screen and (max-width: 1680px)',
		desktopSmall: 'only screen and (max-width: 1440px)',
		laptop: 'only screen and (max-width: 1366px)',
		laptopSmall: 'only screen and (max-width: 1280px)',
		tabletLandscape: 'only screen and (max-width: 1024px)',
		tabletMedium: 'only screen and (max-width: 900px)',
		tabletPortrait: 'only screen and (max-width: 768px)',
		mobileXLarge: 'only screen and (max-width: 640px)',
		mobileLarge: 'only screen and (max-width: 576px)',
		mobileMedium: 'only screen and (max-width: 480px)',
		mobileSmall: 'only screen and (max-width: 415px)',
		mobileXSmall: 'only screen and (max-width: 375px)',
		mobileTiny: 'only screen and (max-width: 325px)'
	},
	colors: {
		red: 'red'
	}
};

export default theme;
```

Now, let's use both of them. To do so, open the `Layout` component file (`src/components/Layout/index.tsx`)

- In there, import both of these files, along with `ThemeProvider` from `styled-components`:

```javascript
// styled components
import { ThemeProvider } from 'styled-components';
import GlobalStyle from '../../styles/GlobalStyle';
import theme from '../../styles/theme';
```

- To use `GlobalStyle`, place it above the Seo component (at the same level
- To use `ThemeProvider`, replace the fragment with it
  - At this point, you should get a red underline
  - This is because ThemeProvider expects a prop called theme
  - So, we can pass in our theme object as value
- In the end, the JSX should look like this:

```javascript
const Layout = ({ children, location }: LayoutProps) => (
	<ThemeProvider theme={theme}>
		<GlobalStyle />
		<Seo title='Gatsby Contentful Blog w/ TypeScript' />
		<Navigation />
		<main className='test'>{children}</main>
		<Footer />
	</ThemeProvider>
);
```

## Automatic Typing for GraphQL Queries

Installed the following packages: `npm i graphql @graphql-codegen/cli` as per https://www.graphql-code-generator.com/docs/getting-started/installation
Created `codegen.yml` file at the root level and pasted in this code:

```yml
schema: http://localhost:8000/___graphql
documents:
  - ./src/**/*.{ts,tsx}
  - ./node_modules/gatsby*/!(node_modules)/**/*.js
generates:
  ./src/types/graphql-types.ts:
    plugins:
      - typescript
      - typescript-operations
    config:
      avoidOptionals: true
      maybeValue: T
      inputMaybeValue: T
      namingConvention:
        enumValues: 'keep'
```

Added this script to package.json: `"types": "graphql-codegen --config codegen.yml"`
Ran the script: `npm run types` and got big error message

Installed 2 more packages: `npm i @graphql-codegen/typescript @graphql-codegen/typescript-operations`
Ran the script again and it generated the file!

## Done!

At this point, we're done all the setup! Whew! That was a lot! Now, it is up to you to build out your project. Some things you can do from here:

- Added custom font (Google Fonts, Adobe Typekit, etc.)
- Add any more desired plugins/npm packages
- Convert existing components using CSS modules to Styled components, or just delete them entirely and start from scratch
- Update GlobalStyle and Theme to your liking

Happy coding!
