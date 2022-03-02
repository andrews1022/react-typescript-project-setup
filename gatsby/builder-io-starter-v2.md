# Gatsby Starter - Builder.io

This is my personal approach for generating a project using the [Builder.io Gatsby Starter](https://github.com/BuilderIO/gatsby-starter-builder) and update it to:

- Use TypeScript
- Use Styled Components
- Deployment to Gatsby Cloud
- Continuous Deployment with Builder.io and Gatsby Cloud

## Outline of Steps to Take

- builder.io setup
  - Create account (if not done already)
  - Create space
  - Click link to generate some models
- gitHub setup
  - Create new repo
  - downloaded starter: `npx gatsby new . https://github.com/BuilderIO/gatsby-starter-builder`
  - removed git from the project: `rm -rf .git`
  - commit everything to github
- cleanup / update starter
  - update npm scripts
  - update packages so we can use typescript

## Builder.io Setup

- Go sign up on Builder.io and create an account [here](https://builder.io/signup)
- Once logged in, create a space
  - Leave it empty
