# Gatsby Starter - Builder.io

This is my personal approach for generating a project using the [Builder.io Gatsby Starter](https://github.com/BuilderIO/gatsby-starter-builder) and update it to:

- Use TypeScript
- Use Styled Components
- Deployment to Netlify
- Continuous Deployment with Builder.io and Netlify

## Builder.io Setup Pt. 1

- Go sign up on Builder.io and create an account [here](https://builder.io/signup) if you don't already have one
  - For any subsequent visits, you can go to your Builder Dashboard [here](https://builder.io/content)
- Once logged in, select `Create a new Builder space`
  - Give it a name, then click `Create`
- In a new tab, go to the GitHub starter page [here](https://github.com/andrews1022/builder-io-gatsby-starter)
  - Click on this [link](https://builder.io/fork-sample-org) to generate some starting models and content

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

## Builder.io Setup Pt. 2

- Go back to your Builder dashboard
- Go to `Account`, stay on the `Space` tab
  - Change the `Site URL` to `http://localhost:8000`
  - Copy the `Public API Key`
- Go to VS Code
  - Open the file `src/config.js`
  - Paste in your API Key
    - We will update to use Environment Variables shortly
- Run the command `npm run develop` to check your project boots up ok

## Netlify

### Initial Setup

Need to setup through the website UI, as `netlify-cli` package causes Builder.io to fail during schema initialization

- Go to your Netlify Dashboard [here](https://app.netlify.com/)
- Click Sites, then `Add new site`, then `Import an existing project`
- Choose `GitHub` as your Git provider
- Select the repo
- Netlify should detect it is a Gatsby site
- Leave settings as-is, then click `Deploy site`

### Continuous Deployment

**NOTE: Recommended to come back to this step LAST as Builder can eat up your Netlify build minutes pretty quickly**

- For continuous deployment between Netlify & Builder.io, we need to do 2 things:
  - Create a [build hook](https://docs.netlify.com/configure-builds/build-hooks/) in Netlify
  - Add the build hook to Builder.io global webhooks in your [space settings](https://builder.io/account/space)
- Netlify Build Hook:
  - Go to the Netlify Dashboard [here](https://app.netlify.com/)
  - Go to `Sites` and select your site
  - Go to `Site settings` > `Build & deploy` > `Continuous deployment`, and then scroll down the `Build hooks` section
  - Click `Add build hook`
    - Give it a name, then click `Save`
    - Copy the generated URL
- Builder.io Space Settings:
  - Go to your Builder.io space settings [here](https://builder.io/account/space)
  - Click the pencil icon for Global `webhooks`
  - Click `+ WEBHOOK`
  - Click on `Webhook 1`
  - Paste in the URL
  - Click `Save`
- Testing the Hook:
  - Go to your Builder.io content [here](https://builder.io/content)
  - Click on `Landing page`, then `Home`
  - Make a simple change to the heading _"We make things great"_, for example
  - Click `PUBLISH UPDATE`
  - Go to the Netlify Dashboard [here](https://app.netlify.com/)
    - Go to Sites and you should see the site is rebuilding!

## Updating the Starter

- Delete `.prettierrc` file
- Update the `README`

### Updating NPM Scripts

- Use this:

```json
"scripts": {
  "build": "npm run clean && gatsby build",
  "clean": "gatsby clean",
  "deploy": "npm run clean && gatsby build --prefix-paths && gh-pages -d public",
  "dev": "npm run clean && gatsby develop",
  "troubleshoot": "rm -rf .cache node_modules public package-lock.json && npm i && npm run dev"
}
```

### Updating NPM Packages

- Uninstall some packages we won't be using:
  - `gh-pages`
  - `prettier`
    - Run the command: `npm un gh-pages prettier`
- Update NPM packages to these versions (do _**NOT**_ prefix version numbers with `^`):

```json
"@builder.io/gatsby": "3.0.0",
"@builder.io/react": "1.1.49",
"@builder.io/widgets": "1.2.21",
"@material-ui/core": "4.12.3",
"gatsby": "4.8.1",
"gatsby-plugin-material-ui": "4.1.0",
"gatsby-plugin-react-helmet": "5.8.0",
"netlify-cli": "9.10.0",
"react": "17.0.2",
"react-dom": "17.0.2",
"react-helmet": "6.1.0",
"react-parallax": "3.3.0"
```

- Updated `createMuiTheme` to `createTheme` in `src/theme.js` as `createMuiTheme` is now deprecated
- Run `npm run troubleshoot`
  - Site should boot up just fine
- Commit this progress: `git add . && git commit -m 'updated npm packages and scripts' && git push -u origin main`

## Adjusting the Starter

- Move `gatsby-browser.js` & `gatsby-ssr.js` from `plugins/gatsby-plugin-top-layout` to the root level
  - Make sure the paths for the imports are correct!
- Delete the `plugins` folder: `rm -rf plugins`
- Remove `gatsby-plugin-top-layout` from the plugins array in `gatsby-config.js`

## Converting to TypeScript

### Setup

- Install the following packages:
  - `concurrently`
  - `dotenv`
  - `gatsby-plugin-typescript`
  - `path`
  - `ts-node`
  - `typescript`
  - `@types/node`
  - `@types/react`
  - `@types/react-dom`
  - `@types/react-helmet`
    - Run the command:
    ```
    npm i concurrently dotenv typescript gatsby-plugin-typescript path ts-node @types/node @types/react @types/react-dom @types/react-helmet
    ```
- Add `gatsby-plugin-typescript` to the plugins array in `gatsby-config.ts`
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

- Update our `scripts` in `package.json` to use `concurrently`:

```json
"dev-gatsby": "gatsby develop",
"dev-typescript": "tsc -w",
"dev": "npm run clean && concurrently \"npm:dev-gatsby\" \"npm:dev-typescript\""
```

- Create `config.ts` inside a `gatsby` folder at the root level: `mkdir gatsby && touch gatsby/config.ts`
- Might need to run the command in VS Code: `TypeScript: Restart TS Server`
  - Hit `Ctrl + Shift + P`, then search for the command

### Converting Files Pt. 1

- Gatsby API Files

  - As of [Gatsby v4.8](https://www.gatsbyjs.com/docs/reference/release-notes/v4.8/#support-for-typescript-in-gatsby-browser-and-gatsby-ssr), we can have `gatsby-browser` and `gatsby-ssr` at the root level use `.tsx` without any extra configuration!
  - `gatsby-browser.tsx` use this code:

  ```tsx
  import React from 'react';
  import type { GatsbyBrowser } from 'gatsby';

  // layouts
  import PageLayout from './src/layouts/PageLayout';
  import RootLayout from './src/layouts/RootLayout';

  export const wrapRootElement: GatsbyBrowser['wrapRootElement'] = ({ element }) => (
    <RootLayout>{element}</RootLayout>
  );

  export const wrapPageElement: GatsbyBrowser['wrapPageElement'] = ({ element }) => (
    <PageLayout>{element}</PageLayout>
  );
  ```

  - `gatsby-ssr.tsx` use this code:

  ```tsx
  import React from 'react';
  import type { GatsbySSR } from 'gatsby';

  // layouts
  import PageLayout from './src/layouts/PageLayout';
  import RootLayout from './src/layouts/RootLayout';

  export const wrapRootElement: GatsbySSR['wrapRootElement'] = ({ element }) => (
    <RootLayout>{element}</RootLayout>
  );

  export const wrapPageElement: GatsbySSR['wrapPageElement'] = ({ element }) => (
    <PageLayout>{element}</PageLayout>
  );
  ```

  - Copy everything in `gatsby-config.js` into `config.ts` in the `gatsby` folder
  - Replace everything in `gatsby-config.js` with just these 2 lines:

  ```ts
  require('ts-node').register();

  module.exports = require('./gatsby/config');
  ```

- Src

  - `builder-settings.ts` use this code:

  ```ts
  import { builder } from '@builder.io/react';

  // a set of widgets you can use in the editor, optional.
  import '@builder.io/widgets';

  // Import all custom components so you can use in the builder.io editor here
  import './components/Hero/Hero.builder';

  import config from './config';
  builder.init(config.builderAPIKey);
  ```

  - `config.ts` in `src` folder use this code:

  ```ts
  export default {
    builderAPIKey: 'YOUR_KEY_HERE'
  };
  ```

  - `theme.ts`: No changes necessary
  - `config.ts` in `gatsby` folder use this code:

  ```ts
  import dotenv from 'dotenv';
  import path from 'path';

  import config from '../src/config';

  dotenv.config({ path: `.env.${process.env.NODE_ENV}` });

  export default {
    pathPrefix: '/gatsby-starter-builder',
    siteMetadata: {
      title: 'Gatsby + Builder.io Starter',
      description:
        'This repo contains an example website that is built with Builder.io, and generate with Gatsby'
    },
    plugins: [
      'gatsby-plugin-typescript',
      'gatsby-plugin-material-ui',
      'gatsby-plugin-react-helmet',
      {
        resolve: '@builder.io/gatsby',
        options: {
          publicAPIKey: config.builderAPIKey,
          templates: {
            // render models with their corresponding template file
            landingPage: path.resolve('src/templates/LandingPage.jsx')
          }
        }
      }
    ]
  };
  ```

- Run `npm run dev` to make sure everything still boots up fine
- Commit this progress: `git add . && git commit -m 'started converting to typescript' && git push -u origin main`

### Converting Files Pt. 2

- Components

  - Hero

    - Delete `.builder.js`
    - Use this in Hero.tsx (no need for separate file):

    ```tsx
    import React from 'react';
    import { Builder, Image } from '@builder.io/react';
    import { Parallax, Background } from 'react-parallax';
    import Button from '@material-ui/core/Button';
    import Typography from '@material-ui/core/Typography';
    import Box from '@material-ui/core/Box';

    type HeroProps = {
      buttonLink: string;
      buttonText: string;
      darkMode: boolean;
      height: number;
      image: string;
      parallaxStrength: number;
      title: string;
    };

    export const Hero = ({
      buttonLink,
      buttonText,
      darkMode,
      height,
      image,
      parallaxStrength,
      title
    }: HeroProps) => {
      return (
        <Parallax
          style={{ height }}
          blur={{ min: -20, max: 20 }}
          bgImageAlt={title}
          strength={parallaxStrength}
        >
          <Box
            style={{ color: darkMode ? 'gray' : 'white' }}
            textAlign='center'
            paddingTop={`calc(${height}px/3)`}
          >
            <Typography variant='h2'>{title}</Typography>
            <Button
              style={{ color: darkMode ? 'gray' : 'white' }}
              variant='outlined'
              href={buttonLink}
            >
              {buttonText}
            </Button>
          </Box>
          <Background>
            {/* Builder optimized image with srcset, lazy, etc */}
            <Image image={image} />
          </Background>
        </Parallax>
      );
    };

    Builder.registerComponent(Hero, {
      name: 'Hero',
      // Optionally give a custom icon (image url - ideally a black on transparent bg svg or png)
      image:
        'https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2Fd6d3bc814ffd47b182ec8345cc5438c0',
      inputs: [
        {
          name: 'title',
          type: 'string',
          defaultValue: 'Your Title Here'
        },
        {
          name: 'image',
          type: 'file',
          allowedFileTypes: ['jpeg', 'jpg', 'png', 'svg'],
          required: true,
          defaultValue:
            'https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F52dcecf48f9c48cc8ddd8f81fec63236'
        },
        {
          name: 'buttonLink',
          type: 'string',
          defaultValue: 'https://example.com'
        },
        {
          name: 'buttonText',
          type: 'string',
          defaultValue: 'Click'
        },
        {
          name: 'height',
          type: 'number',
          defaultValue: 400
        },
        {
          name: 'darkMode',
          type: 'boolean',
          defaultValue: false
        },
        // `advanced: true` hides this option under the "show advanced" toggle
        {
          name: 'parallaxStrength',
          type: 'number',
          advanced: true,
          defaultValue: 400
        }
      ]
    });
    ```

    - Just make sure to update import in `builder-settings.ts`

  - `RenderLink.tsx` use this code:

  ```tsx
  import React from 'react';

  const RenderLink = (props: any) => {
    const { href, target } = props;

    const internal = target !== '_blank' && /^\/(?!\/)/.test(href);

    if (internal) {
      return <a activeClassName='active-link' to={href} {...props} />;
    }

    return <a {...props} />;
  };

  export default RenderLink;
  ```

- Layouts

  - `PageLayout.tsx` use this code:

  ```tsx
  import React from 'react';
  import type { ReactNode } from 'react';
  import { graphql, StaticQuery } from 'gatsby';
  import { BuilderComponent } from '@builder.io/react';
  import { makeStyles } from '@material-ui/core/styles';
  import Link from '../components/Link/Link';
  import '../builder-settings';
  import theme from '../theme';

  type PageLayoutProps = {
    children: ReactNode;
  };

  const useStyles = makeStyles((them) => ({
    root: {
      padding: theme.spacing(1)
    },
    header: {},
    footer: {},
    content: {}
  }));

  const PageLayout = ({ children }: PageLayoutProps) => {
    const classes = useStyles();

    return (
      <StaticQuery query={query}>
        {(data) => {
          const models = data.allBuilderModels;
          const header = models.header[0].content;
          const footer = models.footer[0].content;

          return (
            <div className={classes.root}>
              <div className={classes.header}>
                {/* can either use the builder model, or your own <Navigation /> component here */}
                <BuilderComponent content={header} model='header' renderLink={Link} />
              </div>

              <div className={classes.content}>{children}</div>

              <div className={classes.footer}>
                {/* can either use the builder model, or your own <Footer /> component here */}
                <BuilderComponent content={footer} model='footer' renderLink={Link} />
              </div>
            </div>
          );
        }}
      </StaticQuery>
    );
  };

  export default PageLayout;

  export const query = graphql`
    query {
      allBuilderModels {
        header(limit: 1, options: { cachebust: true }) {
          content
        }
        footer(limit: 1, options: { cachebust: true }) {
          content
        }
      }
    }
  `;
  ```

  - `RootLayout.tsx` use this code:

  ```tsx
  import React from 'react';
  import type { ReactNode } from 'react';
  import { Helmet } from 'react-helmet';
  import CssBaseline from '@material-ui/core/CssBaseline';
  import { ThemeProvider } from '@material-ui/core/styles';
  import theme from '../theme';

  type RootLayoutProps = {
    children: ReactNode;
  };

  const RootLayout = ({ children }: RootLayoutProps) => {
    return (
      <>
        <Helmet>
          <meta charSet='UTF-8' />
          <meta name='theme-color' content='#f8f8f8' />
          <meta name='viewport' content='width=device-width, initial-scale=1.0, user-scalable=no' />
          <meta name='mobile-web-app-capable' content='yes' />
          <meta name='apple-mobile-web-app-capable' content='yes' />
          <meta httpEquiv='X-UA-Compatible' content='ie=edge' />

          {/* add custom fonts from google fonts, adobe typekit, etc. here */}
        </Helmet>
        <ThemeProvider theme={theme}>
          {/* CssBaseline kickstart an elegant, consistent, and simple baseline to build upon. */}
          <CssBaseline />

          {/* this is where PageLayout.tsx is rendered */}
          {children}
        </ThemeProvider>
      </>
    );
  };

  export default RootLayout;
  ```

- Templates

  - `LandingPage.tsx` use this code:

  ```
  import React from 'react';
  import { graphql } from 'gatsby';
  import { BuilderComponent } from '@builder.io/react';
  import { Helmet } from 'react-helmet';
  import Link from '../components/Link/Link';

  type LandingPageTemplateProps = {
    data: any;
  };

  const LandingPageTemplate = ({ data }: LandingPageTemplateProps) => {
    const defaultDescription = 'Edit this in your entry for a better SEO';
    const defaultTitle = 'Builder: Drag and Drop Page Building for Any Site';

    const models = data?.allBuilderModels;
    const landingPage = models.landingPage[0]?.content;

    return (
      <>
        <Helmet>
          <title>{(landingPage && landingPage.data.title) || defaultTitle}</title>
          <meta
            name='description'
            content={(landingPage && landingPage.data.description) || defaultDescription}
          />
        </Helmet>

        {/* this is where each block is rendered on a given page using the 'landing page' model */}
        <BuilderComponent content={landingPage} model='landing-page' renderLink={Link} />
      </>
    );
  };

  export default LandingPageTemplate;

  export const landingPageQuery = graphql`
    query ($path: String!) {
      allBuilderModels {
        landingPage(target: { urlPath: $path }, limit: 1, options: { cachebust: true }) {
          content
        }
      }
    }
  `;
  ```

  - Also make sure to update `.jsx` to `.tsx` in `@builder.io/gatsby` plugin object in `gatsby/config.ts`

- Run `npm run dev` to make sure everything still boots up fine
- Commit this progress: `git add . && git commit -m 'finished converting to typescript' && git push -u origin main`

## Using Environment Variables

- Create these 3 `.env` files at the root level: `touch .env.development .env.production .env.sample`
- Add this to `.gitignore`:

```
# Environment variables
.env
.env.development
.env.production
```

- Add this to each .env file just created: `BUILDER_IO_API_KEY=[YOUR_BUILDER_API_KEY_HERE]`
  - Keep the sample file as it
  - Grab your public API key from either the Builder admin or from `src/config.ts`
  - Replace the placeholder in .env.development & .env.production with the key
- Update plugin object in `gatsby/config.ts`:

```ts
{
  resolve: '@builder.io/gatsby',
  options: {
    publicAPIKey: process.env.BUILDER_IO_API_KEY,
    templates: {
      // render every `page` model as a new page using the src/templates/LandingPage.jsx template
      page: path.resolve('src/templates/Page.jsx')
    }
  }
}
```

- Can now do the following:
  - Remove the config import in this file now
  - Delete `src/config.ts`
  - Remove the config import in `src/builder-settings.ts`
  - Update builder.init() to: `builder.init(process.env.BUILDER_IO_API_KEY!);`
- Commit this progress: `git add . && git commit -m 'updated project to use environment variables' && git push -u origin main`

## ESLint Setup

- Start by running: `npx eslint --init`
- This is how I answer the questions:
  - How would you like to use ESLint? ?? `style`
  - What type of modules does your project use? ?? `esm`
  - Which framework does your project use? ?? `react`
  - Does your project use TypeScript? ?? No / `Yes`
  - Where does your code run? ?? `browser`, `node`
  - How would you like to define a style for your project? ?? `guide`
  - Which style guide do you want to follow? ?? `airbnb`
  - What format do you want your config file to be in? ?? `JSON`
- Download any additional packages if prompted
- Copy and paste in rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)
- Commit this progress: `git add . && git commit -m 'updated project to use eslint' && git push -u origin main`

## Styled Components Setup

We will not only add Styled Components, but also remove Material UI (optional)

- Download and install the following packages:
  - `babel-plugin-styled-components`
  - `gatsby-plugin-styled-components`
  - `styled-components`
  - `@types/styled-components`
- Run the command: `npm i babel-plugin-styled-components gatsby-plugin-styled-components styled-components @types/styled-components`
- Add `gatsby-plugin-styled-components` to the `plugins` array in `gatsby/config.ts`
- Created a new `styles` folder inside `src`
- In the styles folder, create these 2 new files:
  - `GlobalStyle.ts`
  - `theme.ts`
- In `theme.ts`, add this [code](https://gist.github.com/andrews1022/051e113b6ff46a7252bcac1d3b9045fb)
- In `GlobalStyle.ts` added this [code](https://gist.github.com/andrews1022/96c86f6aaa1a12d1332317dffff208f8)
- Update `RootLayout.tsx` to this:

```tsx
import React from 'react';
import { Helmet } from 'react-helmet';
import type { ReactNode } from 'react';

// styled components
import { ThemeProvider } from 'styled-components';
import GlobalStyle from '../styles/Globalstyle';
import theme from '../styles/theme';

type RootLayoutProps = {
  children: ReactNode;
};

const RootLayout = ({ children }: RootLayoutProps) => (
  <>
    <Helmet>
      <meta charSet='UTF-8' />
      <meta name='theme-color' content='#f8f8f8' />
      <meta name='viewport' content='width=device-width, initial-scale=1.0, user-scalable=no' />
      <meta name='mobile-web-app-capable' content='yes' />
      <meta name='apple-mobile-web-app-capable' content='yes' />
      <meta httpEquiv='X-UA-Compatible' content='ie=edge' />

      {/* add custom fonts from google fonts, adobe typekit, etc. here */}
    </Helmet>
    <ThemeProvider theme={theme}>
      <GlobalStyle />

      {/* this is where PageLayout.tsx is rendered */}
      {children}
    </ThemeProvider>
  </>
);

export default RootLayout;
```

- Delete `src/theme.ts`

- Update PageLayout.tsx to:

```tsx
import React from 'react';
import type { ReactNode } from 'react';
import { graphql, StaticQuery } from 'gatsby';
import { BuilderComponent } from '@builder.io/react';
import Link from '../components/Link/Link';
import '../builder-settings';

type PageLayoutProps = {
  children: ReactNode;
};

export const query = graphql`
  query {
    allBuilderModels {
      header(limit: 1, options: { cachebust: true }) {
        content
      }
      footer(limit: 1, options: { cachebust: true }) {
        content
      }
    }
  }
`;

const PageLayout = ({ children }: PageLayoutProps) => (
  <StaticQuery query={query}>
    {(data) => {
      const models = data.allBuilderModels;
      const header = models.header[0].content;
      const footer = models.footer[0].content;

      return (
        <div>
          <div>
            {/* can either use the Header model, or your own <Navigation /> component here */}
            <BuilderComponent content={header} model='header' renderLink={Link} />
          </div>

          {/* this is where LandingPage.tsx is rendered */}
          <div>{children}</div>

          <div>
            {/* can either use the Footer model, or your own <Footer /> component here */}
            <BuilderComponent content={footer} model='footer' renderLink={Link} />
          </div>
        </div>
      );
    }}
  </StaticQuery>
);

export default PageLayout;
```

- Delete the `Hero` component folder (including both files)
- Remove the `Hero` import line in `builder-settings.ts`
- Remove `gatsby-plugin-material-ui` from the plugins array in `gatsby/config.ts`
- Uninstall the material-ui packages: `npm un @material-ui/core gatsby-plugin-material-ui`
- Commit this progress: `git add . && git commit -m 'added styled components and removed material-ui' && git push -u origin main`

## Creating a Custom Code Component

- In `src/components`, create a new folder `Heading`
- In that file, create `index.tsx`
- Copy and paste in the following code:

```tsx
import React from 'react';
import { Builder } from '@builder.io/react';

type HeadingProps = {
  text: string;
};

const Heading = ({ text }: HeadingProps) => <h2>{text}</h2>;

export default Heading;

Builder.registerComponent(Heading, {
  name: 'Heading',
  inputs: [
    {
      name: 'text',
      type: 'string'
    }
  ]
});
```

- Go to `src/builder-settings.ts` and add this just above builder.init():

```ts
// Import all custom components so you can use in the builder.io editor here
import './components/Heading';
```

- Go to Builder page editor
- Should see `Heading` underneath Code Components
- Add it to the page anywhere, then click `Publish Update`
- Shut down local dev server, then start it back up again: `npm run dev`
- Should see the custom `Heading` component on the page (might need to refresh)

## Done!

- From here:
  - Build more reusable components for Builder
  - Create more pages
- It is up to you!
