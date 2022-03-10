# Gatsby Starter - Contentful Homepage

Collection of notes from my experience using [Gatsby Starter Contentful Homepage](https://github.com/gatsbyjs/gatsby-starter-contentful-homepage)

## Setup

- Decided to clear out my pre-existing Contentful space to be safe
- Created new folder: `contentful-homepage-starter`
- Downloaded starter: `npx gatsby new . https://github.com/gatsbyjs/gatsby-starter-contentful-homepage`
- Open in VS Code: `code . `
- Remove git from this project: `rm -rf .git`
- Run setup script: `npm run setup`
  - Entered in Space ID
  - Entered in Content Management API Access Token
  - Entered in Content Delivery API Access Token
  - Script took ~2min to finish
- Start up local dev server (ran clean first): `npm run develop`
  - Booted up just fine

## Post-Setup

- Organize scripts in package.json
- Organize components into their own folders
- Remove any files/folders that are no longer needed
  - `.env.SAMPLE`
  - `pull_request_template.md`
- Create UI folder in components folder, and take every component in ui.js and create own files
  - There are currently **28** components in this 1 file
- Remove props spreading and be more explicit
- Update all files to be either `.js`/`.jsx` or `.ts`/`.tsx` - don't have a mixture of both
- Have a separate all TypeScript version of the starter (did see a PR)
- Optional: Remove react-feather for something like Font Awesome or Material UI Icons, or another icon library
- Optional: Remove @vanilla-extract and replace with Styled Components, Emotion, or other CSS-in-JS library
- Optional: Convert all components to use function expression syntax

## Opinions

- Content is not named the best
  - Should be more reusable
  - Example: "About Profile" should be something more generic like "Team Memember"
  - "About Leadership" parent model should also be changed to something "Team Section" or "Team Grid"
  - Keep the Kicker, Heading, Subheading files
- Would suggest avoiding Content models after pages in general
  - I always approach content models as generic/highly reusable/globally shared
  - What if I want to reuse About Stat List on another page
  - Could confuse users using About Stat List on a different page
  - Why not call it just "Stat List" and use wherever?
- Should not be using && for conditional rendering
  - Should be using ternary operator with desired for 'or' case, or just return null
  - Reasons why explained [here](https://kentcdodds.com/blog/use-ternaries-rather-than-and-and-in-jsx)
- A lot of the code is not spaced enough
  - Example: `src/components/caret.js`
    - should be a line break between the imports and function
    - should also be a line break between `const height ...` and `return`
  - Find this easier to read
- Some content models don't have an Entry Title
  - Example: About Stat List
- Move uses of `useStaticQuery` into their own hooks
  - Shows devs how to use custom hooks
- Think there is too much content being created at the start here
  - Even though I (or at least I would like to think) am quite familiar with Gatsby + Contentful, I found there was too much content to sift through
  - There is a chance I may scrap some non-zero amount of it, or restructure/rename it, which could be a lot of work
  - The existing [contentful/starter-gatsby-blog](https://github.com/contentful/starter-gatsby-blog) I think also has too little content
  - I think there needs to be a nice middle ground, or take this homepage starter and refactor the existing models
  - I think the following models would be nice to start with:
    - Blog Post
      - Can reference an Author model
    - Header (could also be called Nav, Navigation, w/e)
      - Header has a field call Links/Pages, w/e
      - Here, reference Page Models
      - These page models could also have child pages being referenced
      - Then in header.js (or whatever file name), dynamically create the navigation with dropdown menus for child items if they exist
    - Footer
      - Content model from this starter is great
      - Would advise having copyright text as a field
      - Leave it the component and use JSX to dynamically render the current year: `{new Date().getFullYear()}`
    - CTA
      - Has fields for slug and button text
    - Stat List
      - One field you can reference a "Stat" model, an add as many as you like
      - This can be a nice reusable compnent where we want a group of stats
    - Logo List
      - Same idea as Stats List, but with a "Logo" model
    - Cards List
      - Same idea as Stats/Logo List, but with a "Card" model
      - Card model could have fields for image/Icon, Heading, copy, CTA
      - This would be a simplification of the "Homepage Product List" model
    - Page
      - These create the various pages of the site
      - Have a field for "Content" - able to add various blocks/sections as desired
      - This gets kinda sloppy with GraphQL, as you have 1 large query (can be broken up with fragments)
      - Also need to make sure to have a field "ComponentToRender". See my approach [here](https://github.com/andrews1022/contentful-blog-gatsby-starter/tree/dynamic-nav-and-page)
- Who is this for?
  - If you are new to Gatsby / Contentful / React in general, this _might_ be a good starting point
  - If you are experienced, and have your own preferred workflow, you will find yourself spending a lot time initially updating/refactoring both the code and Contentful
  - If you work for say, an agency, you/your team probaby also:
    - Have your own starter that you may or may not maintain
    - Have your go to npm packages/libraries for certain functionality, therefore having to heavily modify this starter
    - Yes you can take this starter and modify it to your teams workflow, and have your own custom starter, or you can start from scratch
    - But if you create your own, you then have to maintain it / update it
