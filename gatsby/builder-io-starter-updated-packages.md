# Gatsby Starter - Builder.io Updated Packages

This is my personal approach for generating a project using the [Builder.io Gatsby Starter](https://github.com/BuilderIO/gatsby-starter-builder) and update it to:

- Use TypeScript
- Use Styled Components
- Deploy to Netlify
- Continuous Deployment with Builder.io and Netlify

## Simplified Steps

- used pre-existing builder.io space
- downloaded starter: `npx gatsby new . https://github.com/BuilderIO/gatsby-starter-builder`
- removed git from the project: `rm -rf .git`
- removed `.prettierrc`
- updated api key in `src/config.js`
- updated `landingPage` to `page` in `gatsby-config.js`
- updated `RootLayout.jsx` to this:

```
import React from 'react';
import { Helmet } from 'react-helmet';
import CssBaseline from '@material-ui/core/CssBaseline';
import { ThemeProvider } from '@material-ui/core/styles';
import theme from '../theme';

const RootLayout = ({ children }) => (
	<>
		<Helmet>
			<meta charSet='UTF-8' />
			<meta name='theme-color' content='#f8f8f8' />
			<meta name='viewport' content='width=device-width, initial-scale=1.0, user-scalable=no' />
			<meta name='mobile-web-app-capable' content='yes' />
			<meta name='apple-mobile-web-app-capable' content='yes' />
			<meta httpEquiv='X-UA-Compatible' content='ie=edge' />
		</Helmet>

		<ThemeProvider theme={theme}>
			{/* CssBaseline kickstart an elegant, consistent, and simple baseline to build upon. */}
			<CssBaseline />

			{/* this is where PageLayout.jsx is rendered */}
			{children}
		</ThemeProvider>
	</>
);

export default RootLayout;
```

- updated PageLayout.jsx to this:

```
import React from 'react';
import { makeStyles } from '@material-ui/core/styles';
import '../builder-settings';
import theme from '../theme';

const useStyles = makeStyles((them) => ({
	root: {
		padding: theme.spacing(1)
	},
	header: {},
	footer: {},
	content: {}
}));

const PageLayout = ({ children }) => {
	const classes = useStyles();

	return (
		<div className={classes.root}>
			<div className={classes.header}>
				<nav>NAV WILL GO HERE</nav>
			</div>

			{/* this is where LandingPage.jsx is rendered */}
			<div className={classes.content}>{children}</div>

			<div className={classes.footer}>
				<footer>FOOTER WILL GO HERE</footer>
			</div>
		</div>
	);
};

export default PageLayout;

```

- renamed `LandingPage.jsx` to `Page.jsx`, and updated code to this:

```
import React from 'react';
import { graphql } from 'gatsby';
import { BuilderComponent } from '@builder.io/react';
import { Helmet } from 'react-helmet';
import Link from '../components/Link/Link';

const PageTemplate = ({ data }) => {
	const defaultDescription = 'Edit this in your entry for a better SEO';
	const defaultTitle = 'Builder: Drag and Drop Page Building for Any Site';

	const models = data?.allBuilderModels;
	const page = models.page[0]?.content;

	return (
		<>
			<Helmet>
				<title>{(page && page.data.title) || defaultTitle}</title>
				<meta name='description' content={(page && page.data.description) || defaultDescription} />
			</Helmet>

			{/** name of the model is landing page, change it if you decided to build*/}
			{/* this is where each block is rendered on a given page from the builder.io ui */}
			<BuilderComponent content={page} model='page' renderLink={Link} />
		</>
	);
};

export default PageTemplate;

export const pageQuery = graphql`
	query($path: String!) {
		allBuilderModels {
			page(target: { urlPath: $path }, limit: 1, options: { cachebust: true }) {
				content
			}
		}
	}
`;
```

- also updated path in gatsby-config to use Page.jsx instead of LandingPage.jsx
- `npm run dev` and site is up and working!
- added new hook, useMenu:

```
import { graphql, useStaticQuery } from 'gatsby';

const useMenu = () => {
	const menu = useStaticQuery(
		graphql`
			query {
				allBuilderModels {
					page {
						id
						name
						data {
							title
							url
						}
					}
				}
			}
		`
	);

	// return the array of objects
	return menu.allBuilderModels.page;
};

export default useMenu;
```

- added new component, `Navigation.jsx`, which uses `useMenu` hook:

```
import React from 'react';
import { Link } from 'gatsby';

// custom hooks
import useMenu from '../../hooks/useMenu';

const Navigation = () => {
	const navLinks = useMenu();

	return (
		<nav>
			<ul>
				{navLinks.map((link) => (
					<li key={link.id}>
						<Link to={link.data.url}>{link.name}</Link>
					</li>
				))}
			</ul>
		</nav>
	);
};

export default Navigation;
```

- updated PageLayout.jsx to use it:

```
<div className={classes.header}>
  <Navigation />
</div>
```

- moved gatsby api files from plugins folder to root level
  - updated import paths
  - removed gatsby-plugin-top-layout from plugins array in gatsby-config
- at this point, included hero builder component was NOT showing up, similar issue to when converting gatsby blog to use builder.io
- **_VERY IMPORTANT_**
  - go to your builder [models](https://builder.io/models)
  - go to page, then change the Editing URL to your localhost
  - THIS IS WHY YOUR CODE COMPONENTS WILL NOT SHOW UP
- updating packages from this:

```
"@builder.io/gatsby": "^1.0.11",
"@builder.io/react": "^1.1.31",
"@builder.io/widgets": "^1.2.0",
"@material-ui/core": "^4.9.7",
"gatsby": "^2.13.31",
"gatsby-plugin-material-ui": "^2.1.6",
"gatsby-plugin-react-helmet": "^3.0.4",
"react": "^16.8.4",
"react-dom": "^16.8.4",
"react-helmet": "^5.2.0",
"react-parallax": "^3.0.3"
```

- to this (using latest versions):

```
"@builder.io/gatsby": "^3.0.0",
"@builder.io/react": "^1.1.49",
"@builder.io/widgets": "^1.2.21",
"@material-ui/core": "^4.12.3",
"gatsby": "^4.8.1",
"gatsby-plugin-material-ui": "^4.1.0",
"gatsby-plugin-react-helmet": "^5.8.0",
"react": "^17.0.2",
"react-dom": "^17.0.2",
"react-helmet": "^6.1.0",
"react-parallax": "^3.3.0"
```

- then ran `npm run troubleshoot` to start fresh
  - all packages downloaded without issue
  - updated `createMuiTheme` to `createTheme` in `src/theme.js`
- converting to typescript...

  - IT WORKS!
  - able to create Copy.tsx and it works just fine
  - was able to convert useMenu hook to typescript:

  ```
  import { graphql, useStaticQuery } from 'gatsby';

  type GraphQLResult = {
    allBuilderModels: {
      page: {
        id: string;
        name: string;
        data: {
          title: string;
          url: string;
        };
      }[];
    };
  };

  const useMenu = () => {
    const menu = useStaticQuery<GraphQLResult>(
      graphql`
        query {
          allBuilderModels {
            page {
              id
              name
              data {
                title
                url
              }
            }
          }
        }
      `
    );

    // return the array of objects
    return menu.allBuilderModels.page;
  };

  export default useMenu;
  ```

- update project to use env variables:

  - create .env file at root level

  ```
  BUILDER_IO_API_KEY=[BUILDER_IO_PUBLIC_API_KEY_HERE]
  ```

  - install following packages: `npm i dotenv path`
  - replace these imports:

  ```
  const path = require('path');
  const config = require('./src/config');
  ```

  - with these imports:

  ```
  const path = require('path');
  const dotenv = require('dotenv');

  dotenv.config();
  ```

  - then update plugin object in gatsby-config:

  ```
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

- at this point, while site would load, error would show in console and in builder.io that api key had not been set
- following documentation [here](https://www.gatsbyjs.com/docs/how-to/local-development/environment-variables/)

  - created `.env.development` and `.env.production`
  - copied and pasted `BUILDER_IO_API_KEY=[BUILDER_IO_PUBLIC_API_KEY_HERE]` into both
  - updated path and dotenv at the top of gatsby-config to:

  ```
  const path = require('path');

  require('dotenv').config({
    path: `.env.${process.env.NODE_ENV}`
  });
  ```

- ran `npm run dev` to start up fresh, no more errors!
  - since we are not accessing this env var in the browser, we did not need to prefix the env var with `GATSBY_`
- can now delete `src/config.js`, and remove the import line in `src/builder-settings.js`

- update project to use styled components:

  - installed the following packages: `npm i babel-plugin-styled-components gatsby-plugin-styled-components styled-components`
  - added `'gatsby-plugin-styled-components'`, to `plugins` array in `gatsby-config.js`
  - created new folder `styles` inside `src`
  - added 2 new files:
    - GlobalStyle.js
    - theme.js
      - command: `touch GlobalStyle.js theme.js`
  - in theme.js, added this code:

  ```
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
  			lightCoral: '#f08080'
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

  - in GlobalStyle.js added this code:

  ```
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

  - updated RootLayout.jsx to this:

  ```
  import React from 'react';
  import { Helmet } from 'react-helmet';

  // styled components
  import { ThemeProvider } from 'styled-components';
  import GlobalStyle from '../styles/GlobalStyle';
  import theme from '../styles/theme';

  const RootLayout = ({ children }) => (
  	<>
  		<Helmet>
  			<meta charSet='UTF-8' />
  			<meta name='theme-color' content='#f8f8f8' />
  			<meta name='viewport' content='width=device-width, initial-scale=1.0, user-scalable=no' />
  			<meta name='mobile-web-app-capable' content='yes' />
  			<meta name='apple-mobile-web-app-capable' content='yes' />
  			<meta httpEquiv='X-UA-Compatible' content='ie=edge' />
  		</Helmet>

  		<ThemeProvider theme={theme}>
  			<GlobalStyle />

  			{/* this is where PageLayout.jsx is rendered */}
  			{children}
  		</ThemeProvider>
  	</>
  );

  export default RootLayout;
  ```
