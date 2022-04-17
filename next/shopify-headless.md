# # Next.js + Shopify

This doc will show you how to setup a project using Next.js and Shopify as a headless ecommerce platform. We will use the following:

- Next.js
- Shopify
- TypeScript
- Styled Components
- GraphQL

## Boilerplate

- Download the latest Next.js TypeScript starter: `npx create-next-app@latest --ts`
- Remove any lock files and reinstall packages with npm: `rm yarn.lock package-lock.json && npm i`
- Create a `components` and `types` folder: `mkdir components types`
- Start local dev server and check everything is running ok: `npm run dev`
- Quickly go through each file and format on save with Prettier.
- Commit this progress: `git add . && git commit -m 'project setup' && git push -u origin main`

## Shopify Setup

- Go to [Shopify Partners](https://www.shopify.com/partners) and create an account
  - Next time you can login [here](https://partners.shopify.com/organizations)
- Once in your dashboard, click `Add Store`
  - Click `Development Store`
  - Enter a `Store Name`
  - Enter in your account password
  - Select `I'm just playing around` for Store purpose
  - Wait for store setup to finish