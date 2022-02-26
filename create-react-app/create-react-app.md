# Create React App

This is my personal approach for generating a project using React, TypeScript, & Styled Components using Create React App.

## GitHub Setup

- Create a new GitHub repo [here](https://github.com/new)
  - Give it at least a name, then click `Create repository`
  - Keep this tab open
- On your local machine, create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
  - Where `PROJECT_NAME` matches the name of the GitHub repo you just created
- Download the Create React App TypeScript template: `npx create-react-app . --template typescript`
- After that is done, open it in VS Code: `code .`
- Remove git from this project (so we can start from scratch): `rm -rf .git`
- Run all the git commands as outlined on the repo page to commit this progress:

```
git init && git add . && git commit -m "project setup" && git branch -M main && git remote add origin https://github.com/GITHUB_USERNAME/PROJECT_NAME.git && git push -u origin main
```

## NPM Packages

- We only need a couple of extra packages for this setup:
  - [Netlify CLI](https://www.npmjs.com/package/netlify-cli)
  - [Styled Components](https://www.npmjs.com/package/styled-components)
- Download & install the following packages: `npm i netlify-cli styled-components @types/styled-components`

## Netlify Setup

- Authenticate and obtain an access token by running: `npx netlify login`
- Init a new Netlify site: `npx netlify init`
  - Select `Create & configure a new site`
  - Select your team
  - Give the site a name (or leave blank, as it can always be renamed later)
  - Choose `Authorize with GitHub through app.netlify.com`
    - Click Authorize where needed
    - Any other time this step will be done automatically and the CLI will go directly to the next step
  - Set `Your build command` to: `npm run build`
  - Set `Directory to deploy` to: `build`
  - Leave Netlify functions blank (just hit `Enter`)
  - Select `n` to not add the `netlify.toml` file
- Go to the Netlify Dashboard [here](https://app.netlify.com/)
  - Go to Sites and make sure the site was successfully deployed
  - The build can take a few minutes, so you will have to wait!
- Commit this progress: `git add . && git commit -m 'setup netlify continuous integration' && git push -u origin main`

## CRA Cleanup

- Go into the `src` folder:
  - Remove these files: `rm App.css App.test.tsx index.css logo.svg reportWebVitals.ts setupTests.ts`
- Go into the `public` folder:
  - Remove these files: `rm logo192.png logo512.png manifest.json`
- All that should be left are `App.tsx`, `index.tsx`, and `react-app-env.d.ts`
- Copy and paste this code into `index.tsx`:

```javascript
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

```javascript
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
  - Copy and paste in rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)
- Commit this progress: `git add . && git commit -m 'added eslint' && git push -u origin main`

## Styled Components Setup

- In the `src` folder, create the following folders (at least):
  - `components`
  - `styles`
  - `types`
    - Run the command: `mkdir components styles types`
- Go into the `styles` folder:
  - Create the following 3 files:
    - `GlobalStyle.ts` (for global styling)
    - `lib.ts` (for shared styling)
    - `theme.ts` (for theme)
      - Run the command: `touch GlobalStyle.ts lib.ts theme.ts`
- Copy and paste this code into `lib.ts`:

```javascript
// a library of reusable styled components / base styles to be used anywhere in the project

import styled, { css } from 'styled-components';
```

- Copy and paste this code into `theme.ts`:

```javascript
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

- Copy and paste this code into `GlobalStyle.ts`:

```javascript
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

- Go to the components folder:
  - Here, create a new `App` folder: `mkdir App`
  - Next, go back to the `src` directory
  - Now, move `App.tsx` into the `App` directory and rename it to `index.tsx`: `mv App.tsx components/App/index.tsx`
- Go back to `index.tsx` in `src` and copy and paste this code:

```javascript
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
