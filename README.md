# React, Styled Components & TypeScript Setup

This is my personal approach for generating a project using React, Styled Components & TypeScript.

1. Create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
2. Createa a react-typescript boilerplate project using CRA: `npx create-react-app . --template typescript`
3. After downloading, install `styled-components` packages: `npm i styled-components @types/styled-components`
4. Go into the `src` folder: `cd src`
5. Cleanup unnecessary files: `rm App.css App.test.tsx index.css logo.svg reportWebVitals.ts setupTests.ts`
6. Go into the `public` folder: `cd public`
7. Cleanup unnecessary files: `rm logo192.png logo512.png manifest.json`
8. All that should be left are `App.tsx`, `index.tsx`, and `react-app-env.d.ts`
9. Copy and paste this code into `index.tsx`:

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

10. Copy and paste this code into `App.tsx`:

```
import React from 'react';

const App = () => (
  <div>
    <h1>Hello from App.tsx!</h1>
  </div>
);

export default App;

```

11. Add ESLint: `npx eslint --init`

- Go through and answer the questions accordingly
- Pick `JSON` for the file format
- Download any additional packages if prompted
- Run `npm audit fix` if prompted
- Copy and paste in rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)

12. In the `src` folder, create the following folders (at least):

- `components`
- `styles`
- `types`

Run the command: `mkdir components styles types`

13. Go into the `styles` folder: `cd styles`
14. In the `styles` folder, create the following 3 files:

- `GlobalStyle.ts` (for global styling)
- `lib.ts` (for shared styling)
- `theme.ts` (for theme)

Run the command: `touch GlobalStyle.ts lib.ts theme.ts`

15. Copy and paste this code into `lib.ts`:

```
// a library of re-usable styled components / base styles to be used anywhere in the project

import styled, { css } from "styled-components";
```

16. Copy and paste this code into `theme.ts`:

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
  },
  spacing: {
    // add spacing variables here
  }
};

export default theme;
```

17. Copy and paste this code into `GlobalStyle.ts`:

```
import { createGlobalStyle } from "styled-components";
import theme from "./theme";

// destructured theme properties
const { mediaQueries, fonts, fontWeights, spacing } = theme;

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

18. Go back to `index.tsx` in `src` and update the code to look like this:

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

For Next.js, see [here](https://github.com/andrews1022/vancouver-shop-local-react-typescript/blob/main/pages/_app.tsx).

For Gatsby, wrap like so in `Layout.tsx`:

```
return (
  <div data-is-root-path={isRootPath} onClick={closeGetInTouchPopupOnBodyClick} role="presentation">
    <ThemeProvider theme={theme}>
      <GlobalStyle />
      <Nav isRootPath={isRootPath} location={location} />
      <GetInTouchPopup reference={popupRef} setToggle={setToggle} toggle={toggle} />
      <main className="main">{children}</main>
      <Footer />
    </ThemeProvider>
  </div>
);
```

Done!
