# Create React App

This is my personal approach for generating a project using React, Styled Components & TypeScript using Create React App.

## GitHub Setup

- Create a new repo on GitHub [here](https://github.com/new)
  - Give it at least a name, then click `Create repository`
  - Keep this tab open for all the git commands
- Create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
- Create a react-typescript boilerplate project using CRA: `npx create-react-app . --template typescript`
- Open in VS Code: `code .`
- Remove git from this project (to start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page
  - Here it is in one command:
    `git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/USERNAME/PROJECT_NAME.git && git push -u origin main`

## Netlify Setup

- At the root level of the project, install the Netlify CLI: `npm i netlify-cli`
- Authenticate and obtain an access token `npx netlify login`
- Init a new Netlify site: `npx netlify init`

  - Select `Create & configure a new site`
  - Select your team
  - Give the site a name (or leave blank, can be renamed later)
  - Choose `Authorize with GitHub through app.netlify.com`
    - Click Authorize where needed
  - Set your build command to: `npm run build`
  - Set your build directory to: `build`
  - Leave Netlify functions blank (just hit `Enter`)
  - Select `n` to not add the `netlify.toml` file

- Commit this progress: `git add . && git commit -m 'setup netlify continuous integration' && git push -u origin main`

## CRA Cleanup

- Go into the `src` folder: `cd src`
  - Remove these files if not needed: `rm App.css App.test.tsx index.css logo.svg reportWebVitals.ts setupTests.ts`
- Go into the `public` folder: `cd public`
  - Remove these files if not needed: `rm logo192.png logo512.png manifest.json`
- All that should be left are `App.tsx`, `index.tsx`, and `react-app-env.d.ts`
- Copy and paste this code into `index.tsx`:

```
import React, { StrictMode } from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <StrictMode>
    <App />
  </StrictMode>,
  document.getElementById('root')
);
```

- Copy and paste this code into `App.tsx`:

```
import React from 'react';

const App = () => (
  <div>
    <h1>Hello from App.tsx!</h1>
  </div>
);

export default App;

```

- Commit this progress: `git add . && git commit -m 'updated boilerplate files' && git push -u origin main`

## ESLint Setup

- Add ESLint: `npx eslint --init`

  - Go through and answer the questions accordingly
  - Pick `JSON` for the file format
  - Download any additional packages if prompted
  - Run `npm audit fix` if prompted
  - Copy and paste in rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)

- Commit this progress: `git add . && git commit -m 'added eslint' && git push -u origin main`

## Styled Components Setup

- Downloading & install Styled Components packages: `npm i styled-components @types/styled-components`
- In the `src` folder, create the following folders (at least):
  - `components`
  - `styles`
  - `types`
- Run the command: `cd src && mkdir components styles types`

- Go into the `styles` folder: `cd styles`

  - Create the following 3 files:
    - `GlobalStyle.ts` (for global styling)
    - `lib.ts` (for shared styling)
    - `theme.ts` (for theme)
  - Run the command: `cd styles && touch GlobalStyle.ts lib.ts theme.ts`

- Copy and paste this code into `lib.ts`:

```
// a library of re-usable styled components / base styles to be used anywhere in the project

import styled, { css } from "styled-components";
```

- **NOTE**: This file is sometimes not used, so it can always be deleted later on

- Copy and paste this code into `theme.ts`:

```
const theme = {
  mediaQueries: {
    desktopHD: "only screen and (max-width: 1920px)",
    desktopMedium: "only screen and (max-width: 1680px)",
    desktopSmall: "only screen and (max-width: 1440px)",
    laptop: "only screen and (max-width: 1366px)",
    laptopSmall: "only screen and (max-width: 1280px)",
    tabletLandscape: "only screen and (max-width: 1024px)",
    tabletMedium: "only screen and (max-width: 900px)",
    tabletPortrait: "only screen and (max-width: 768px)",
    mobileXLarge: "only screen and (max-width: 640px)",
    mobileLarge: "only screen and (max-width: 576px)",
    mobileMedium: "only screen and (max-width: 480px)",
    mobileSmall: "only screen and (max-width: 415px)",
    mobileXSmall: "only screen and (max-width: 375px)",
    mobileTiny: "only screen and (max-width: 325px)"
  },
  shades: {
    white: "#fff",
    black: "#000"
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
    black: 900,
  },
  fontSizes: {
    // add font sizes here
  }
};

export default theme;
```

- Copy and paste this code into `GlobalStyle.ts`:

```
import { createGlobalStyle } from "styled-components";
import theme from "./theme";

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

- Commit this progress: `git add . && git commit -m 'setup styled components' && git push -u origin main`

## Final Adjustments

- Go to the components folder: `cd .. && cd components`

  - In here, create a new folder for App: `mkdir App`

- Now, move `App.tsx` into this directory and rename it to `index.tsx`:

  - First, `cd` back to the `src` directory
  - From here, move the file to the App folder and rename it: `mv App.tsx components/App/index.tsx`

- Go back to `index.tsx` in `src` and copy and paste this code:

```
import React, { StrictMode } from 'react';
import ReactDOM from 'react-dom';

// styled components
import { ThemeProvider } from 'styled-components';
import GlobalStyle from './styles/GlobalStyle';
import theme from './styles/theme';

// components
import App from './components/App';

ReactDOM.render(
	<StrictMode>
		<ThemeProvider theme={theme}>
			<GlobalStyle />
			<App />
		</ThemeProvider>
	</StrictMode>,
	document.getElementById('root')
);
```

- Go to `index.html` in the `public` folder

  - Remove the comments
  - Update the `title` tag
  - Add Google Fonts (or whichever fonts) in here
  - Update `theme.fonts` in `theme.ts`, then update the comment placeholders in `GlobalStyle.ts` with the new font

- Check the project runs ok locally:

  - `cd` back to root of the project
  - Run `npm run start`

- Update the README (remove CRA related content)
- Commit this progress: `git add . && git commit -m 'final setup adjustments' && git push -u origin main`
