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

- If you run `npm run dev` to start the local development server, you will get an error: `Error: Contentful spaceId and the access token need to be provided.`. This starter conviently comes with a Contentful setup script.
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
