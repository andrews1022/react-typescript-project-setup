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
  - Get rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/tree/main/rules)
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
- In `theme.ts` add the following [code](https://gist.github.com/andrews1022/051e113b6ff46a7252bcac1d3b9045fb)
- In `GlobalStyle.ts` add the following [code](https://gist.github.com/andrews1022/96c86f6aaa1a12d1332317dffff208f8)
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
