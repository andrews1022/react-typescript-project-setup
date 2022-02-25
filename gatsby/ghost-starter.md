# Gatsby Starter - Ghost

This is my personal approach for generating a project using the [Ghost Gatsby Starter](https://github.com/TryGhost/gatsby-starter-ghost) and update it to:

- Use TypeScript
- Use Styled Components
- Deploy to Netlify

## Ghost Setup

## GitHub Setup

- Create a new repo on GitHub [here](https://github.com/new)
  - Give it at least a name, then click `Create repository`
  - Keep this tab open
- On your local machine, create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
  - Where `PROJECT_NAME` matches the name of the GitHub repo you just created
- Download the starter: `npx gatsby new . https://github.com/TryGhost/gatsby-starter-ghost`
- After that is done, open it in VS Code: `code .`
- Remove git from this project (to start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page to commit this progress:

```
git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/GITHUB_USERNAME/PROJECT_NAME.git && git push -u origin main
```

## Project Setup With Known Steps to Correct

- clone the project using git: `git clone https://github.com/TryGhost/gatsby-starter-ghost.git`
- delete:
  - .github
  - plugins/gatsby-plugin-ghost-manifest/yarn.lock
  - .editorconfig
  - netlify.toml
  - renovate.json
  - yarn.lock
    - command: `rm -rf .github plugins/gatsby-plugin-ghost-manifest/yarn.lock .editorconfig netlify.toml renovate.json yarn.lock`
- updated version numbers in package.json
  - "react": "17.0.2" --> "16.9.0"
  - "react-dom": "17.0.2" --> "16.9.0"
  - "gatsby": "3.12.1" --> "3.13.1"
- add this package:
  - "url": "0.11.0"
- moved all devDependencies to dependencies for easier maintanence
- ran npm i at root level
- ran `npm run dev` again

- optional: ran `npm i` in `plugins/gatsby-plugin-ghost-manifest` folder

## Post Setup

- changed .eslint.js --> .eslint.json
- went to ghost dashboard, created new content api key
  - settings > integrations > custom integration
- updated .ghost.json with new apiUrl and contentApiKey
- also updated gatsby-config with options as per docs
- added page in ghost admin, published, didn't show up in localhost
- stopped dev server, ran npm run dev (which also runs clean first), and it showed up!

## Post Setup To Do

- use updated versions of eslint and related packages
