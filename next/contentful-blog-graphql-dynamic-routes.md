# Next

This is my personal approach for generating a Blog using Next.js, Styled Components, TypeScript, & Contentful w/ GraphQL, and host on Vercel, with dyanmic routes and content.

## Boilerplate

- Download the latest Next.js TypeScript starter: `npx create-next-app@latest --ts`
- Remove any lock files and reinstall packages with npm: `rm yarn.lock package-lock.json && npm i`
- Create a `components` and `types` folder: `mkdir components types`
- Start local dev server and check everything is running ok: `npm run dev`
- Quickly go through each file and format on save with Prettier.
- Commit this progress: `git add . && git commit -m 'project setup' && git push -u origin main`

## Custom ESLint

- Remove current `.eslintrc.json` and uninstall existing ESLint packages: `rm .eslintrc.json && npm un eslint eslint-config-next`
- Initialize ESLint: `npx eslint --init` or `npm init @eslint/config`
- Answer the questions accordingly, and select yes to install any peer dependencies
- Once done, open `.eslintrc.json`
  - ESLint may not be applying yet, so might have to close and reopen in VS Code
  - Get rules from [here](https://github.com/andrews1022/eslint-react-quick-setup/blob/main/rules/create-react-app.json)
  - Make sure to add `"plugin:@typescript-eslint/recommended"` to `"extends"` array
  - Will need to import React at the top of existing files
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

  - If you don't have a `Layout` component yet, just replace with an empty `fragment` for now
  - Hit `Ctrl + .` on any of the red underlined code and select `Add all missing imports`

- Remove the `.css` import from `_app.tsx` as well
- Setup server-side rendered styles to the head

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
    static getInitialProps = async (ctx: DocumentContext): Promise<DocumentInitialProps> => {
      const sheet = new ServerStyleSheet();
      const originalRenderPage = ctx.renderPage;

      try {
        ctx.renderPage = () =>
          originalRenderPage({
            enhanceApp: (App) => (props) => sheet.collectStyles(<App {...props} />)
          });

        const initialProps = await Document.getInitialProps(ctx);

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
          <Head>{/* add google fonts here */}</Head>
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
- Commit this progress: `git add . && git commit -m 'added styled components and removed default css files & imports' && git push`

## Querying for Contentful Data Using GraphQL

- Use Contentful GraphiQL playground here: `https://graphql.contentful.com/content/v1/spaces/YOUR_SPACE_ID_HERE/explore?access_token=YOUR_ACCESS_TOKEN_HERE`
  - Just make sure to update the placeholders with your actual Contentful Space ID and Access Token.
- Recommend install the [`graphql-request`](https://www.npmjs.com/package/graphql-request) package: `npm i graphql-request`
- Create `.env.local` & `.env.sample` at the root level and add the following to each:

```
CONTENTFUL_SPACE_ID=CONTENTFUL_SPACE_ID_HERE
CONTENTFUL_ACCESS_TOKEN=CONTENTFUL_CONTENT_DELIVERY_API_ACCESS_TOKEN_HERE
```

- Just replace the placeholder values with your `Space ID` and `Content Delivery API - Access Token` in `.env.local`
- To have Next.js load these variables, simply restart the dev server
  - You should see `info - Loaded env from ...` in the terminal
- Next we need to whitelist the Contentful domain for Next.js to optimize images. Update next.config.js to this:

  ```js
  /** @type {import('next').NextConfig} */
  const nextConfig = {
    images: {
      domains: ['images.ctfassets.net']
    },
    reactStrictMode: true
  };

  module.exports = nextConfig;
  ```

  - Restart the dev server after this change

- In pages/index.tsx, add the following above the Component:

  ```ts
  export const getStaticProps = async () => {
    // get contentful data
    const endpoint = `https://graphql.contentful.com/content/v1/spaces/${process.env.CONTENTFUL_SPACE_ID}`;

    const options = {
      headers: {
        authorization: `Bearer ${process.env.CONTENTFUL_ACCESS_TOKEN}`
      }
    };

    const graphQLClient = new GraphQLClient(endpoint, options);

    const homePageQuery = gql`
      query HomepageQuery {
        // query here
      }
    `;

    const data: ContentfulResponse = await graphQLClient.request(homePageQuery);

    return {
      props: {
        data
      }
    };
  };
  ```

  - This structure assumes that:

    - You are querying for multiple things
    - The type ContentfulResponse is just above this function and would look something like this:

    ```ts
    type ContentfulResponse = {
      aboutMe: ContentfulAboutMe;
      hero: ContentfulHero;
      projectGroup: ContentfulProjectGroup;
    };
    ```

- Now update the prop types for the Home page component:

```ts
type HomeProps = {
  data: ContentfulResponse;
};

const Home: NextPage<HomeProps> = ({ data }) => {
  // ...
};
```

- Then pass the data down to components like so:

```jsx
<Hero contentfulData={data.hero} />
```

## Querying for Contentful Data Using GraphQL - No Dependencies

We'll perform the same above but using just the Fetch API

```ts
type ContentfulData = {
  aboutMe: ContentfulAboutMe;
  contact: ContentfulContact;
  hero: ContentfulHero;
  projectGroup: ContentfulProjectGroup;
};

type ContentfulResponse = {
  data: ContentfulData;
};

// neat trick for getting better formatting inside template string
const gql = String.raw;

export const getStaticProps = async () => {
  const response = await fetch(
    `https://graphql.contentful.com/content/v1/spaces/${process.env.CONTENTFUL_SPACE_ID}`,
    {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${process.env.CONTENTFUL_ACCESS_TOKEN}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        query: gql`
          query HomepageQuery {
            # query here...
          }
        `
      })
    }
  );

  const { data }: ContentfulResponse = await response.json();

  return {
    props: {
      data
    }
  };
};

type HomeProps = {
  data: ContentfulData;
};

const Home: NextPage<HomeProps> = ({ data, posts }) => {
  console.log('data: ', data);

  return(
    // ...
  );
};
```

## Dynamic Routes & Pages

Let's assume we have a content model of `Project` in Contentful and we want to dynamically create individual pages for each.

We'll also assume each `Project` has a `slug` field.

- In the pages folder, create a new project folder
  - Then in this folder, create a file: `[slug].tsx`
  - Using the square brackets [ ] with a variable, in this case `slug`, tells Next.js to use Dyanmic Routing and a dynamic routed pages
- In this file we'll need both `getStaticProps()` and `getStaticPaths()`

  - Start with this empty functions for now:

  ```ts
  export const getStaticPaths = async () => {
    return {};
  };

  export const getStaticProps = async () => {
    // get contentful data

    return {
      props: {}
    };
  };
  ```

- Let's update `getStaticPaths()` first:

  - First, let's make our request to Contentful:

  ```ts
  export const getStaticPaths = async () => {
    // get contentful data
    const response = await fetch(
      `https://graphql.contentful.com/content/v1/spaces/${process.env.CONTENTFUL_SPACE_ID}`,
      {
        headers: {
          Authorization: `Bearer ${process.env.CONTENTFUL_ACCESS_TOKEN}`,
          'Content-Type': 'application/json'
        },
        method: 'POST',
        body: JSON.stringify({
          query: gql`
            query ProjectPageQuery {
              projectCollection {
                items {
                  slug
                }
              }
            }
          `
        })
      }
    );

    const { data }: ContentfulResponse = await response.json();

    return {};
  };
  ```

  - Here, we only need to retrieve the slug

  - Next, we want to create an array of objects with the following structure:

  ```ts
  {
    params: {
      [KEY]: [VALUE]
    }
  }
  ```

  - Here, `[KEY]` should be the same value as inside your `[ ]` for the file name.
    - In this case, that would be `slug`
  - And [VALUE] should be the `slug` from Contentful
  - So, we can map over our data, and return an array objects that looks like this:

  ```ts
  const slugs = data.projectCollection.items.map((item) => ({ params: { slug: item.slug } }));
  ```

  - Finally, in the return for `getStaticPaths()`, we want to return an object with at least these 2 properties: `paths` and `fallback`:

  ```ts
  return {
    paths: slugs,
    fallback: false // show 404 page
  };
  ```

  - Here, we set `paths` to our `slugs` array
  - And we set `fallback` to `false`. Setting it to `false` will simply show a 404 page instead if the `slug` is incorrect
    - Docs on `getStaticPaths()` [here](https://nextjs.org/docs/basic-features/data-fetching/get-static-paths)

- Now lets's update `getStaticProps()`:

  - First, `getStaticProps()` can actually receive a destructured `{ params }` object:

    ```ts
    export const getStaticProps = async ({ params }: any) => {
      // get contentful data

      return {
        props: {}
      };
    };
    ```

    - We'll update the type of `any` on the `params` in a moment

  - Now we can access `params.slug `like so:

  ```ts
  export const getStaticProps = async ({ params }: any) => {
    // get contentful data
    const { slug } = params;

    return {
      props: {}
    };
  };
  ```

  - Now we need to create a GraphQL query where we query for a single project by it's `slug`. We do so on the `collection`, _not_ on a single project, like so (also good idea to set limit to 1 just in case):

  ```ts
  query SingleProjectQuery {
    projectCollection(where: { slug: "SLUG_HERE" }, limit: 1) {
      items {
        title
      }
    }
  }
  ```

  - Now `getStaticProps()` should look like this:

  ```ts
  export const getStaticProps = async ({ params }: any) => {
    // get contentful data
    const { slug } = params;

    // get contentful data
    const response = await fetch(
      `https://graphql.contentful.com/content/v1/spaces/${process.env.CONTENTFUL_SPACE_ID}`,
      {
        headers: {
          Authorization: `Bearer ${process.env.CONTENTFUL_ACCESS_TOKEN}`,
          'Content-Type': 'application/json'
        },
        method: 'POST',
        body: JSON.stringify({
          query: gql`
            query SingleProjectQuery {
              projectCollection(where: { slug: "prenuvo" }, limit: 1) {
                items {
                  title
                }
              }
            }
          `
        })
      }
    );

    const { data }: ContentfulPropsResponse = await response.json();

    return {
      props: {
        data
      }
    };
  };
  ```

  - Now let's update the query to use GraphQL variables:

  ```ts
  type ContentfulPropsResponse = {
    data: {
      projectCollection: {
        items: {
          title: string;
        }[];
      };
    };
  };

  const response = await fetch(
    `https://graphql.contentful.com/content/v1/spaces/${process.env.CONTENTFUL_SPACE_ID}`,
    {
      headers: {
        Authorization: `Bearer ${process.env.CONTENTFUL_ACCESS_TOKEN}`,
        'Content-Type': 'application/json'
      },
      method: 'POST',
      body: JSON.stringify({
        query: gql`
          query SingleProjectQuery($slug: String!) {
            projectCollection(where: { slug: $slug }, limit: 1) {
              items {
                title
              }
            }
          }
        `,
        variables: {
          slug
        }
      })
    }
  );

  const { data }: ContentfulPropsResponse = await response.json();
  ```

  - Now we can be clever and destructure the one and only project in array and return that via props:

  ```ts
  const [projectData] = data.projectCollection.items;

  return {
    props: {
      projectData
    }
  };
  ```

  - And finally, we can access this projectData prop in your component and display the project title dynamically like so:

  ```ts
  type ProjectPageProps = {
    projectData: {
      title: string;
    };
  };

  const ProjectPage: NextPage<ProjectPageProps> = ({ projectData }) => (
    <div>
      <Head>
        <title>{projectData.title} | Andrew Shearer Dev Portfolio</title>
        <meta name='description' content={projectData.title} />
      </Head>

      <h2>Project Page</h2>

      <p>
        This is the project page for <strong>{projectData.title}</strong>
      </p>
    </div>
  );

  export default ProjectPage;
  ```

  - You can see we also use the `<Head />` component from Next to dynamically set the title and meta description of the page for SEO purposes

- Now let's update the types for the 2 getStatic functions

  - Thankfully, Next gives us the types `GetStaticPaths` & `GetStaticProps`
  - We can add these to our import like so:

  ```ts
  import type { GetStaticPaths, GetStaticProps, NextPage } from 'next';
  ```

  - We can set the type of getStaticPaths() to GetStaticPaths, and do the same for getStaticProps with its respective type:

  ```ts
  export const getStaticPaths: GetStaticPaths = async () => {
    // ...
  };

  export const getStaticProps: GetStaticProps = async ({ params }) => {
    // ...
  };
  ```

  - However, after doing this, you'll see a type error here in `getStaticProps()`:

  ```ts
  export const getStaticProps: GetStaticProps = async ({ params }) => {
    // Property 'slug' does not exist on type 'ParsedUrlQuery | undefined'
    const { slug } = params;
  };
  ```

  - Note the first line of the `getStaticProps`. Here we are attempting to access the `slug` variable that was created in `getStaticPaths` and returned inside the `params` object. This is the line that causes the error as `params` has the type `ParsedUrlQuery | undefined` and `slug` does not exist in `ParsedUrlQuery`:
    - `const { slug } = params`
  - To fix this, simply create an interface that extends `ParsedUrlQuery` and contains a `slug` property:

  ```ts
  interface IParams extends ParsedUrlQuery {
    slug: string;
  }
  ```

  - Side note, if you wish to extend an interface to a type, you can do so like this:

  ```ts
  type IParams = ParsedUrlQuery & {
    slug: string;
  };
  ```

  - Examples can be seen [here](https://stackoverflow.com/questions/37233735/interfaces-vs-types-in-typescript#answer-52682220)

  - Then import ParsedUrlQuery from `querystring`:

  ```ts
  import type { ParsedUrlQuery } from 'querystring';
  ```

  - This solution was found [here](https://wallis.dev/blog/nextjs-getstaticprops-and-getstaticpaths-with-typescript)
