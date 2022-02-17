# Gatsby Starter - Contentful Blog

This document covers how you can start with the Contentful Blog Gatsby Starter and update it to:

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
    - styles.css
  /Container
    - index.js
  /Footer
    - index.js
    - styles.css
  etc.

```

Again, this is just my personal approach that I've always used. Totally up to you how you want to organize things.

So, let's get started!

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

At this point, we are no longer using `lodash`. In fact, we are no longer using any of the following packages:

- `contentful-import`
- `gh-pages`
- `lodash`
- `netlify-cli`

So, stop the local development server (Ctrl + C), and then we can uninstall them all by running `npm un contentful-import gh-pages lodash netlify-cli`.

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
