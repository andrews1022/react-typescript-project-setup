# Gatsby Starter - Contentful Blog

This document covers how you can start with the [Contentful Blog Gatsby Starter](https://github.com/contentful/starter-gatsby-blog) and update it to:

- Use TypeScript
- Use Styled Components
- Host on Gatsby Cloud
- Continuous Integration with GitHub

## Contentful Setup

- Login to Contentful [here](https://be.contentful.com/login)
- Go to Settings > API keys
  - Make sure you are on the `Content delivery / preview tokens` tab
  - Click `Add API key` button in the top right corner
  - Give it a name at least, and also description
  - Click Save

## GitHub Setup

- Create a new repo on GitHub [here](https://github.com/new)
  - Give it at least a name, then click `Create repository`
  - Keep this tab open for all the git commands
- Create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
- Download the starter: `npx gatsby new . https://github.com/contentful/starter-gatsby-blog`
- Open in VS Code: `code .`
- Remove git from this project (to start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page
  - Here it is in one command:
    `git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/USERNAME/PROJECT_NAME.git && git push -u origin main`

## Project Setup - Contentful

If you run `npm run dev` to start the local development server at this point in time, you will get an error: `Error: Contentful spaceId and the access token need to be provided.`. This starter conviently comes with a Contentful setup script.

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
- Once complete, now you can run `npm run dev` to start the local development server

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
  - Click `Save`
- Click `Build site`
  - The build can take a few minutes, so you will have to wait!

## Testing the Workflow Pt. 1 - Simple Code Change

Let's test our workflow by making a simple change in the code. This will prove that whenever we make a commit / push to GitHub, it will automatically trigger a re-build of the site on Gatsby Cloud.

- In VS Code, go to `/src/components/article-preview.js`
- Find this piece of JSX: `<h2 className={styles.title}>{post.title}</h2>`
- Simply add on a few exclaimation points after `{post.title}`: `<h2 className={styles.title}>{post.title}!!</h2>`
- Commit the change: `git add . && git commit -m 'quick commit for testing workflow' && git push -u origin main`
- Go to your Gatsby Dashboard
  - This should've triggered a re-build of the site
  - You might need to just refresh the page

## Testing the Workflow Pt. 2 - Simple Contentful Change

Let's test our workflow some more by making a simple change in the Contentful backend. The setup script created some starter models and content for us, so we will make a simple change to the Person content "John Doe". This will prove that whenever we make a change in Contentful, it will automatically trigger a re-build of the site on Gatsby Cloud.

- Go to the Contentful Space
- Go to the Content tab, and click on the John Doe piece of Content
- Make a simple change, like to the name
- Go to your Gatsby Dashboard
  - The build time for this is typically VERY quick, ~3-4 seconds
  - You should see the change on the site

So, at this point, we have confirmed whenever we either:

- A) Make a change in the code and commit / push to GitHub
- B) Make a change in the Contentful backend

Gatsby Cloud will automatically trigger a re-build of the site, keeping it up-to-date at all times.

## Starter Cleanup Pt. 1 - Removing Unnecessary Files & Folders

Let's take a quick moment to cleanup the starter code, as we no longer need some of the files / folders.

After some testing, here is a list of the files folders we can & cannot deleted post-setup:

✓ --> CAN be removed

✕ --> CANNOT be removed

- [✓] .cache --> can be deleted, but is regenerated each time you rebuild, and is ignored by git anyways
- [✓] /bin & package.json scripts --> used for running `npm run dev` to setup contentful
- [✓] /contentful --> used for running `npm run dev` to setup contentful
- [✓] /node_modules --> can be deleted, but is regenerated each time you install packages, and is ignored by git anyways
- [✓] /public --> can be deleted, but is regenerated each time you rebuild, and is ignored by git anyways
- [✕] /src --> essential
- [✕] /static --> used to house files like robots.txt and favicon
- [✓] \_config.yml --> used for GitHub pages
- [✕] .babelrc --> babel config file
- [✓] .contentful.json.sample --> sample contentful data file
- [✕] .gitignore --> used to intentionally ignore/not track specific files/folders
- [✕] .npmrc --> configuration file for NPM, defines the settings on how NPM should behave when running commands
- [✕] .nvmrc --> specify which node version the project should use
- [✓] .prettierrc --> config for prettier, this is entirely subjective so up to you (i use prettier settings in vs code)
- [✓] .travis.yml --> config file for Travis CI (a hosted continuous integration service)
- [✓] app.json --> unsure, not used anywhere
- [✕] gatsby-config.js --> will convert to typescript later on
- [✕] gatsby-node.js --> will convert to typescript later on
- [✕] LICENSE --> ok to leave, no harm
- [✓] package-lock.json --> can be deleted, but is regenerated each time you install packages
- [✕] package.json --> essential
- [✕] README.md --> essential
- [✓] screenshot.png --> was used in the README, no longer needed
- [✓] static.json --> unsure, not used anywhere, possibly used for Heroku
- [✓] WHATS-NEXT.md --> Simple markdown file

You may remove the files / folders marked with a '✓' as you wish.

After that, let's do a quick restart/rebuild of the local server to make sure everything is still running ok: `rm -rf .cache public && npm run dev`

You should also add that command to your scripts in package.json, as it does come in handy: `"rebuild": "rm -rf .cache public && npm run dev"`

As well as this one: `"troubleshoot": "rm -rf .cache node_modules public package-lock.json && npm i && npm run dev"`

At this point, I personally encountered a git error along the lines of `Fatal unable to access, could not resolve host` when trying to commit changes. If this happens, it's likely a proxy issue. Simply run this command and it should fix the problem:

`git config --global --unset http.proxy && git config --global --unset https.proxy`

## Starter Cleanup Pt. 2 - Components & Pages

Somewhat frustratingly, the starter uses a mix of Classes and Functions for components & pages. Let's convert all the files using classes to use the function syntax. This makes it easier when we convert the files to TypeScript later on since everything is consistent.

The list of files we need to adjust are:

- `src/components/layout.js`
- `src/pages/blog.js`
- `src/pages/index.js`
- `src/templates/blog-post.js`

Furthermore, all the component files use kebab-case for naming. Personally, I prefer to use PascalCase (as you do in CRA or Next apps). So I will update all file names to use PascalCase instead. I understand that they are likely all kebab-case to be consistent with the pages and templates, this just a personal preference.

DO NOT rename page files to use PascalCase. Gatsby uses the file name for routing, so if you change blog.js to Blog.js, the route will no longer work.

Lastly, I will group each component and it's CSS module file together in a folder to keep things a big organized. The file/folder structure will now be:

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

Again, this is just my personal approach that I've always used. Totally up to you how you want to organize things.

Later on when we setup Styled Components, we will replace each `*.module.css` file with a `styles.ts` file. This file will house any styled components used by the functional component in the same folder. So, the structure then will be:

```
/components
  /ArticlePreview
    - index.tsx
    - styles.ts
  /Container
    - index.tsx
  /Footer
    - index.tsx
    - styles.ts
  etc.

```

So, I will not bother renaming the CSS module files since they will be replaced anyways.

Alright, let's get started!

### Layout.js

Replace this code:

```
class Template extends React.Component {
	render() {
		const { children } = this.props;

		return (
			<>
				<Seo />
				<Navigation />
				<main>{children}</main>
				<Footer />
			</>
		);
	}
}

export default Template;
```

With this code:

```
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

### Blog.js

Replace this code:

```
class BlogIndex extends React.Component {
	render() {
		const posts = get(this, 'props.data.allContentfulBlogPost.nodes');

		return (
			<Layout location={this.props.location}>
				<Seo title='Blog' />
				<Hero title='Blog' />
				<ArticlePreview posts={posts} />
			</Layout>
		);
	}
}
```

With this code:

```
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

Pages access the data returned from the `graphql` query via the `data` prop. We use this approach for the remaining files

### RootIndex.js

Replace this code:

```
class RootIndex extends React.Component {
	render() {
		const posts = get(this, 'props.data.allContentfulBlogPost.nodes');
		const [author] = get(this, 'props.data.allContentfulPerson.nodes');

		return (
			<Layout location={this.props.location}>
				<Hero
					image={author.heroImage.gatsbyImageData}
					title={author.name}
					content={author.shortBio.shortBio}
				/>
				<ArticlePreview posts={posts} />
			</Layout>
		);
	}
}

export default RootIndex;
```

With this code:

```
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

### BlogPostTemplate.js

Replace this code:

```
class BlogPostTemplate extends React.Component {
	render() {
		const post = get(this.props, 'data.contentfulBlogPost');
		const previous = get(this.props, 'data.previous');
		const next = get(this.props, 'data.next');

		return (
			<Layout location={this.props.location}>
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
	}
}
```

With this code:

```
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

At this point, we are no longer using `lodash`. In fact, we are no longer using any of the following packages:

- `contentful-import`
- `gh-pages`
- `lodash`
- `netlify-cli`

So, stop the local development server (`Ctrl + C`), and then we can uninstall them all by running: `npm un contentful-import gh-pages lodash netlify-cli`.

We can also simply our `scripts` in `package.json` to:

```
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

First, go into the components folder: `cd src/components/`

Next, we need to create all folders for each component:

- ArticlePreview
- Container
- Footer
- Hero
- Layout
- Navigation
- Seo
- Tags

We can create them all at once by running: `mkdir ArticlePreview Container Footer Hero Layout Navigation Seo Tags`

Now, one at a time, move the corresponding files into their folders. Hopefully VS Code automatically updates the import path(s) for you. If not, you will have to manually update them yourself.

After moving everything around, you should see the following warning: `warn chunk commons [mini-css-extract-plugin]`

This error/warning is caused by the Webpack plugin mini-css-extract-plugin wanting all CSS imports to be in the same order. This is because it confused CSS modules with plain CSS.

However, since we will be using Styled Components, we can ignore this warning and continue. If you wish to continue using CSS Modules, simply google `warn chunk commons mini-css-extract-plugin gatsby` and you will find plenty of results on troubleshooting the warning.

## Converting to TypeScript

Now comes a BIG step: converting everything to TypeScript! We will convert all components, pages, and even the Gatsby API files (gatsby-config, gatsby-node, etc.) at the root level to use TypeScript.

Gatsby claims to support TypeScript out of the box. That is partially true. If we create a simple Copy component:

```
const Copy = () => (
	<p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Error, nesciunt?</p>
);
```

And use it in ArticlePreview above the tags, it will work just fine. However, we don't get proper type checking. VS Code will highlight the error, but Gatsby CLI will not.

Here is a summary of the steps we will take:

- Install all the necessary packages
- Setup tsconfig.json
- Convert all the components & pages to TypeScript
- Convert the Gatsby API files to use TypeScript

<!--
  Packages installed:
  - concurrently
  - typescript

  Type packages:
  - @types/react-helmet
-->

Starting off:

- First start by installing typescript: `npm i typescript`
- Create a tsconfig file: `tsc --init`
- Select everything in tsconfig.json, and replace it with this:

```
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

Next, we need to install `concurrently` which will also to run multiple scripts, well, concurrently: `npm i concurrently`

Next, we need to update our `scripts` in `package.json`:

```
"dev-gatsby": "gatsby develop",
"dev-typescript": "tsc -w",
"dev": "concurrently \"npm:dev-gatsby\" \"npm:dev-typescript\"",
```

At this point, we should run our rebuild script to start fresh: `npm run rebuild`

Now we will also see any type errors in the CLI!

### Pages

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
  ```
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

  ```
  export type BlogPost = {
    title: string;
    slug: string;
    publishDate: string;
    tags: string[];
    heroImage: {
      gatsbyImageData: IGatsbyImageData;
    };
    description: {
      childMarkdownRemark: {
        html: string;
      };
    };
  };

  export type Person = {
    name: string;
    shortBio: {
      shortBio: string;
    };
    title: string;
    heroImage: {
      gatsbyImageData: IGatsbyImageData;
    };
  };
  ```

  - These will be our re-usable types throughout the project
  - Back in the `index.tsx`, adjust the `GraphQLResult` type to:

  ```
  type GraphQLResult = {
    allContentfulBlogPost: {
      nodes: BlogPost[];
    };
    allContentfulPerson: {
      nodes: Person[];
    };
  };
  ```

  - Now, we can pass this type in as an additional argument to PageProps:

  ```
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

  ```
  export type SingleBlogPost = {
    author: {
      name: string;
    };
    body: {
      childMarkdownRemark: {
        html: string;
        timeToRead: number;
      };
    };
    description: {
      childMarkdownRemark: {
        excerpt: string;
      };
    };
    heroImage: {
      gatsbyImageData: IGatsbyImageData;
      resize: {
        src: string;
      };
    };
    publishDate: string;
    rawDate: string;
    slug: string;
    tags: string[];
    title: string;
  };

  export type NextPrevious = { slug: string; title: string } | null;
  ```

  - Back in the `blog-post.tsx`, adjust the `GraphQLResult` type to:

  ```
  type GraphQLResult = {
    contentfulBlogPost: SingleBlogPost;
    next: NextPrevious;
    previous: NextPrevious;
  };
  ```

  - Then pass it to `PageProps`:

  ```
  const BlogPostTemplate = ({ data, location }: PageProps<GraphQLResult>) => {
    // ...
  };
  ```

### Components

Now let's update the components to .tsx! The steps are similar to the pages:

- Rename `index.js` to `index.tsx`
- Setup type for the props (if any)

  - Example:

  ```
  // props
  type ArticlePreviewProps = {
    posts: BlogPost[];
  };

  const ArticlePreview = ({ posts }: ArticlePreviewProps) => {
    // ...
  };
  ```

- If you are having trouble/unsure how to type the pre-existing components, you can see how I did so [here](https://github.com/andrews1022/gatsby-contentful-blog/tree/main/src/components).

## Styled Components Setup

- To use ThemeProvider, wrap `Layout.tsx` like so:

```
return (
  <div data-is-root-path={isRootPath} onClick={closeGetInTouchPopupOnBodyClick} role="presentation">
    <ThemeProvider theme={theme}>
      <GlobalStyle />
      <Nav isRootPath={isRootPath} location={location} />
      <GetInTouchPopup reference={popupRef} setToggle={setToggle} toggle={toggle} />
      <main className="main">{children}</main>
      <Footer />
    </ThemeProvider>
  </div>
);
```
