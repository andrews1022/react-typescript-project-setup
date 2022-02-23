# Gatsby Starter - Builder.io

This is my personal approach for generating a project using the [Builder.io Gatsby Starter](https://github.com/BuilderIO/gatsby-starter-builder) and update it to:

- Use TypeScript
- Use Styled Components

## Builder.io Setup

- Create a free account with Builder.io [here](https://builder.io/signup) first if you don't already have one
- Once in your dashboard, click "Create a new Builder space" and give it a name
- Then click this [link](https://builder.io/fork-sample-org) to generate some basic starter content for your space

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

## Starter Cleanup

- Update README
- Update package.json scripts (shorten develop to dev)
- Uninstall material-ui packages
- Remove unnecessary files & folders
- Update builder-io plugin in gatsby-config to use environment variable instead of importing api key
