# Next.js

This is my personal approach for getting a Next.js project up an running using TypeScript and Styled Components with custom ESLint Config.

## Boilerplate

- Download the latest Next.js TypeScript starter: `npx create-next-app@latest --ts`
- Remove any lock files and reinstall packages with npm: `rm yarn.lock package-lock.json && npm i`
- Create a `components` and `types` folder: `mkdir components types`
- Start local dev server and check everything is running ok: `npm run dev`
- Quickly go through each file and format on save with Prettier
- Create a [new repo on GitHub](https://github.com/new), and then commit this progress: `git add . && git commit -m 'project setup' && git push -u origin main`

## Custom ESLint (Optional)

- Remove current `.eslintrc.json` and uninstall existing ESLint packages: `rm .eslintrc.json && npm un eslint eslint-config-next`
- Initialize ESLint: `npx eslint --init` or `npm init @eslint/config`
- Answer the questions accordingly, and select yes to install any peer dependencies
- Once done, open `.eslintrc.json`
  - ESLint may not be applying yet, so might have to close and reopen in VS Code
  - Get rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)
  - Make sure to add `"plugin:@typescript-eslint/recommended"` to `"extends"` array
- You can add on these 2 rules if you are using React v17 or later:

```json
"react/jsx-uses-react": "off",
"react/react-in-jsx-scope": "off",
```

- Commit this progress: `git commit -am 'added custom eslint rules' && git push`

## Styled Components

- Install the following packages:
  - `babel-plugin-styled-components`
  - `styled-components`
  - `@types/styled-components`
- Run the command: `npm i babel-plugin-styled-components styled-components @types/styled-components`
- In the styles folder, create 2 new files:
  - `GlobalStyle.ts`
  - `theme.ts`
- Run the command: `cd styles && touch GlobalStyle.ts theme.ts && cd ..`
- In `theme.ts` add the following:

  ```ts
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
    shades: {
      white: '#fff',
      black: '#000'
    },
    greys: {
      // add any greys here
    },
    colors: {
      primary: {
        // add primary colors here
      },
      secondary: {
        // add secondary colors here
      }
    },
    fonts: {
      // add font-families here
    },
    fontWeights: {
      thin: 100,
      extraLight: 200,
      light: 300,
      normal: 400,
      medium: 500,
      semiBold: 600,
      bold: 700,
      extraBold: 800,
      black: 900
    },
    fontSizes: {
      // add font sizes here
    }
  };

  export default theme;
  ```

- In `GlobalStyle.ts` add the following:

  ```ts
  import { createGlobalStyle } from 'styled-components';
  import theme from './theme';

  // destructured theme properties
  const { mediaQueries } = theme;

  const GlobalStyle = createGlobalStyle`
    html {
      box-sizing: border-box;
      font-size: 100%;
  
      @media ${mediaQueries.desktopSmall} {
        font-size: 87.5%;
      }
    }
  
    body {
      /* add custom font-family here */
      line-height: 1;
    }
  
    *,
    *::before,
    *::after {
      box-sizing: inherit;
      color: inherit;
      font-size: inherit;
      -webkit-font-smoothing: antialiased;
      margin: 0;
      padding: 0;
    }
  
    button,
    input,
    select,
    textarea {
      /* add custom font-family here */
    }
  
    img,
    svg {
      border: 0;
      display: block;
      height: auto;
      max-width: 100%;
    }
  
    a {
      &:link,
      &:visited {
        text-decoration: none;
      }
  
      @media (hover) {
        &:hover,
        &:active,
        &:focus {
          outline: 0;
          text-decoration: underline;
        }
      }
    }
  
    ol,
    ul {
      list-style: none;
    }
  
    blockquote,
    q {
      quotes: none;
    }
  
    blockquote:before,
    blockquote:after,
    q:before,
    q:after {
      content: '';
      content: none;
    }
  
    table {
      border-collapse: collapse;
      border-spacing: 0;
    }
  
    audio,
    canvas,
    video {
      display: inline-block;
      max-width: 100%;
      zoom: 1;
    }
  `;

  export default GlobalStyle;
  ```

- Open `pages/_app.tsx`

  - Update the component function to this:

  ```ts
  const MyApp = ({ Component, pageProps }: AppProps) => (
    <ThemeProvider theme={theme}>
      <Layout>
        <GlobalStyle />
        <Component {...pageProps} />
      </Layout>
    </ThemeProvider>
  );

  export default MyApp;
  ```

  - Hit `Ctrl + .` on any of the red underlined code and select `Add all missing imports`

- Remove the `.css` import from `_app.tsx` as well

- We need to setup server-side rendered styles to the head

  - Create a file `_document.tsx` in the `pages` folder
  - Run the command: `cd pages && touch _document.tsx && cd ..`
  - In the `_document.tsx`, add this:

  ```tsx
  /* eslint-disable react/jsx-props-no-spreading */

  // next
  import Document, { Head, Html, Main, NextScript } from 'next/document';
  import type { DocumentContext, DocumentInitialProps } from 'next/document';

  // styled components
  import { ServerStyleSheet } from 'styled-components';

  class MyDocument extends Document {
    static getInitialProps = async (ctx: DocumentContext): Promise<any> => {
      const sheet = new ServerStyleSheet();
      const originalRenderPage = ctx.renderPage;

      try {
        ctx.renderPage = () =>
          originalRenderPage({
            enhanceApp: (App) => (props) => sheet.collectStyles(<App {...props} />)
          });

        const initialProps = await Document.getInitialProps(ctx);

        // can't use DocumentInitialProps as Promise type argument otherwise styles throws an error
        return {
          ...initialProps,
          styles: (
            <>
              {initialProps.styles}
              {sheet.getStyleElement()}
            </>
          )
        };
      } finally {
        sheet.seal();
      }
    };

    render() {
      return (
        <Html>
          <Head>
            <link rel='preconnect' href='https://fonts.googleapis.com' />
            <link rel='preconnect' href='https://fonts.gstatic.com' crossOrigin='true' />
            <link
              href='https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap'
              rel='stylesheet'
            />
          </Head>
          <body>
            <Main />
            <NextScript />
          </body>
        </Html>
      );
    }
  }

  export default MyDocument;
  ```

- Remove the default `.css` files `globals.css` & `Home.module.css`:
  - `cd styles && rm globals.css Home.module.css && cd ..`
- Go into `pages/index.tsx` and remove the `.module.css` import and any existing classNames using the module

- You might run into an error saying the `className` prop is not the same on Server (see the console).

  - To fix this, create a .babelrc file at the root level: `touch .babelrc`
  - In this file, add the following:

  ```json
  {
    "presets": ["next/babel"],
    "plugins": [["styled-components", { "ssr": true }]]
  }
  ```

  - Restart the dev server, and the error will go away

- Commit this progress: `git add . && git commit -m 'added styled components and removed default css files & imports' && git push`
