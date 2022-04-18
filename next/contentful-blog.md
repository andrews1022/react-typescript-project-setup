# Next.js + Contentful Blog

This doc will show you how to generate a Blog using Next.js, Styled Components, TypeScript, & Contentful w/ GraphQL, and host on Vercel, with dyanmic routes and content using Static Site Generation.

The following will be covered:

- Setting up Next.js Boilerplate
- Contentful
- Custom ESLint (Optional)
- Basic Components
- Styled Components
- Basic Custom Types
- Querying for Contentful Data Using GraphQL & Fetch API
- BlogPosts Component
- Dynamic Routes & Pages
- Typing the `getStatic` Functions
- Display the Markdown Content
- GraphQL Fragments
- Update Styling
- Displaying the Author
- Creating Custom `NextImage` Component
- Adding Date Published and Time To Read
- Adding Preview Text
- Adding Categories
- Creating & Displaying `RelatedPosts`
- Host on Vercel

To Do:

- Refactoring:
  - Upate `Layout` to take props
  - Create reusable GraphQL Query Function that is typed
  - Create reusable `Wrapper` styled component

## Boilerplate

- Download the latest Next.js TypeScript starter: `npx create-next-app@latest --ts`
- Remove any lock files and reinstall packages with npm: `rm yarn.lock package-lock.json && npm i`
- Create a `components` and `types` folder: `mkdir components types`
- Start local dev server and check everything is running ok: `npm run dev`
- Quickly go through each file and format on save with Prettier.
- Commit this progress: `git add . && git commit -m 'project setup' && git push -u origin main`

## Contentful

- Login to Contentful [here](https://be.contentful.com/login)
- Create an `Author` Content Model with the following fields:
  - Name (Entry title)
  - Bio
  - Image
- Create a Blog Post Model with the following fields:
  - Title
  - Slug (short text, slug, and base off entry title)
  - Image (used for preview card and hero)
  - Content (Markdown text box)
  - Author (reference to Author Model)
- Create some dummy Blog posts, or real ones, whichever you prefer
  - You can grab some dummy Markdown content from [here](https://markdown-it.github.io/)

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

## Basic Components

Here we'll setup a few very basic Layout based components:

- `Footer`
- `Nav`
- `Layout`
  - Run the command: `cd components && touch Footer.tsx Nav.tsx Layout.tsx && cd ..`

`Footer.tsx`:

```tsx
const Footer = () => (
  <footer style={{ backgroundColor: 'lightblue' }}>
    <p>
      &copy; {new Date().getFullYear()} all rights reserved <span> | </span> designed and built by
      andrew shearer
    </p>
  </footer>
);

export default Footer;
```

`Nav.tsx`:

```tsx
import Link from 'next/link';

const Nav = () => (
  <header style={{ backgroundColor: 'lightcoral' }}>
    <strong style={{ marginRight: '2rem' }}>Contentful Next.js Blog</strong>

    <Link href='/'>
      <button type='button'>Go Back Home</button>
    </Link>
  </header>
);

export default Nav;
```

`Layout.tsx`:

```ts
import Head from 'next/head';
import type { ReactNode } from 'react';
import Footer from './Footer';
import Nav from './Nav';

type LayoutProps = {
  children: ReactNode;
};

const Layout = ({ children }: LayoutProps) => (
  <div className='layout-wrapper'>
    <Head>
      <title>Andrew Shearer Dev Portfolio</title>
      <meta name='description' content="Andrew Shearer's front end developer portfolio" />
      <link rel='icon' href='/favicon.ico' />
    </Head>

    <Nav />

    <main>{children}</main>

    <Footer />
  </div>
);

export default Layout;
```

Also in the `Layout` component, we've moved the `<Head>` from `pages/index.tsx` to here. This way the `Head` will be the same on all pages.

You can also update it to take a `title` prop of type string and pass in a dynamic value for dynamic pages or hard coded value on pages like an About Us or Contact Us page.

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
- Commit this progress: `git add . && git commit -m 'added styled components and removed default css files & imports' && git push`

## Basic Custom Types

We'll create some reusable custom types so we can type our data from Contentful and get nice type checking for our components

- Create a `types` folder: `mkdir types`
- Go into this folder and create a `contentful.ts` file: `cd types && touch contentful.ts && cd ..`
- In that file, add the following:

```ts
// reusable
export type ContentfulImage = {
  description: string;
  height: number;
  sys: {
    id: string;
  };
  url: string;
  width: number;
};

// sections
export type ContentfulAuthor = {
  bio: string;
  image: ContentfulImage;
  name: string;
};

export type ContentfulBlogPost = {
  author: ContentfulAuthor;
  content: string;
  image: ContentfulImage;
  slug: string;
  sys: {
    id: string;
  };
  title: string;
};
```

## Querying for Contentful Data Using GraphQL & Fetch API

- Use Contentful GraphiQL playground here: `https://graphql.contentful.com/content/v1/spaces/YOUR_SPACE_ID_HERE/explore?access_token=YOUR_ACCESS_TOKEN_HERE`
  - Just make sure to update the placeholders with your actual Contentful Space ID and Access Token.
- You can install the [`graphql-request`](https://www.npmjs.com/package/graphql-request) package: `npm i graphql-request`
  - _**However**_, we will use plain [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) so to not introduce extra dependencies
- Create `.env.local` & `.env.sample` at the root level: `touch .env.local .env.sample`

  - Add the following to each:

  ```
  CONTENTFUL_SPACE_ID=CONTENTFUL_SPACE_ID_HERE
  CONTENTFUL_ACCESS_TOKEN=CONTENTFUL_CONTENT_DELIVERY_API_ACCESS_TOKEN_HERE
  ```

  - Just replace the placeholder values with your `Space ID` and `Content Delivery API - Access Token` in `.env.local`

- To have Next.js load these variables, simply restart the dev server

  - You should see `info - Loaded env from ...` in the terminal

- Next we need to whitelist the Contentful domain for Next.js to optimize images. Update `next.config.js` to this:

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

  - Restart the dev server after making this change

- We can create a nice and simple utility `gql.ts` which will give us nice formatting and syntax highlighting the template strings. You _will_ need the [GraphQL extension for VS Code](https://marketplace.visualstudio.com/items?itemName=GraphQL.vscode-graphql) for it work, though.

  - Create a utils folder and then create `gql.ts` inside that folder: `mkdir utils && cd utils && touch gql.ts && cd ..`
  - In `gql.ts`, add just this one line of code:

  ```ts
  export const gql = String.raw;
  ```

  - Now we can import and use this in front of any GraphQL query template string, which we'll see just below

- In `pages/index.tsx`, have the following:

```ts
type ContentfulResponse = {
  data: {
    blogPostCollection: {
      items: ContentfulBlogPost[];
    };
  };
};

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
            blogPostCollection {
              items {
                image {
                  description
                  height
                  sys {
                    id
                  }
                  url
                  width
                }
                slug
                sys {
                  id
                }
                title
              }
            }
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
  data: ContentfulResponse;
};

const Home: NextPage<HomeProps> = ({ data, posts }) => {
  console.log('data: ', data);

  return <BlogPosts contentfulData={data.blogPostCollection.items} />;
};
```

## BlogPosts Component

We don't have a BlogPosts component, so let's create it quickly. This component will simply render all the preview cards on the homepage:

- Create the file: `cd components && touch BlogPosts.tsx && cd ..`
- In this file, add the following:

```ts
import Link from 'next/link';

// custom types
import type { ContentfulBlogPost } from '../types/contentful';

type BlogPostsProps = {
  contentfulData: ContentfulBlogPost[];
};

// destructure and rename to posts
const BlogPosts = ({ contentfulData: posts }: BlogPostsProps) => {
  console.log('posts: ', posts);

  return (
    <div>
      <h2>Blog Posts</h2>

      {posts.map((post) => (
        <div key={post.sys.id}>
          <h3>{post.title}</h3>

          <button style={{ color: 'blue' }} type='button'>
            <Link href={`/blog/${post.slug}`}>Read Post</Link>
          </button>
        </div>
      ))}
    </div>
  );
};

export default BlogPosts;
```

Now we can click on the `Read Post` button to take us to the individual blog post. However, we will see a 404 page. Let's fix that next.

## Dynamic Routes & Pages

Now we want to create the dynamic routes & page template for our Blog Posts.

- In the `pages` folder, create a new `blog` folder

  - Then in this `blog` folder, create a file: `[slug].tsx`
  - Using the square brackets `[ ]` with a variable, in this case `slug`, tells Next.js to use Dyanmic Routing and a dynamic routed pages
  - Run the command: `cd pages && mkdir blog && cd blog && touch [slug].tsx && cd .. && cd ..`

- In this file we'll need both `getStaticProps()` and `getStaticPaths()`

  - Start with these empty functions for now:

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

  - Let's make our request to Contentful. Here we will request all the blog posts and grab just the `slugs`:

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
            query BlogPostSlugsQuery {
              blogPostCollection {
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

  - Next, we want to create an array of objects with the following structure:

  ```ts
  {
    params: {
      [KEY]: [VALUE]
    }
  }
  ```

  - Here, `[KEY]` should be the same value as inside your `[ ]` for the file name. In this case, that would be `slug`
  - And `[VALUE]` should be the `slug` from Contentful
    - NOTE: This structure _**MUST**_ be followed
  - So, we can `map` over our data, and return an array objects that looks like this:

  ```ts
  const slugs = data.blogPostCollection.items.map((item) => ({ params: { slug: item.slug } }));
  ```

  - Finally, in the return for `getStaticPaths()`, we want to return an object with at least these 2 properties: `paths` and `fallback`:

  ```ts
  return {
    paths: slugs,
    fallback: false // show 404 page
  };
  ```

  - Here, we set `paths` to our `slugs` array and we set `fallback` to `false`
    - Setting it to `false` will simply show a 404 page instead if the `slug` is incorrect
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

  - Now we need to create a GraphQL query where we query for a single blog post by it's `slug`. We do so on the `collection`, _not_ on a single blog post, like so (also good idea to set limit to 1 just in case):

  ```ts
  query SingleBlogPostQuery {
    blogPostCollection(where: { slug: "SLUG_HERE" }, limit: 1) {
      items {
        title
      }
    }
  }
  ```

  - Now `getStaticProps()` should look like this (we'll fetch just the `title` for now):

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
            query SingleBlogPostQuery {
              blogPostCollection(where: { slug: "prenuvo" }, limit: 1) {
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
      blogPostCollection: {
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
          query SingleBlogPostQuery($slug: String!) {
            blogPostCollection(where: { slug: $slug }, limit: 1) {
              items {
                author {
                  bio
                  image {
                    description
                    height
                    sys {
                      id
                    }
                    url
                    width
                  }
                  name
                }
                content
                image {
                  description
                  height
                  sys {
                    id
                  }
                  url
                  width
                }
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

  - Now we can be clever and destructure the one and only blog post in array and return that via props:

  ```ts
  const [blogPostData] = data.blogPostCollection.items;

  return {
    props: {
      blogPostData
    }
  };
  ```

  - And finally, we can access this `blogPostData` prop in your component and display the blog post `title` dynamically like so:

  ```ts
  // props type
  type BlogPostPageProps = {
    blogPostData: ContentfulBlogPost;
  };

  const BlogPostPage: NextPage<BlogPostPageProps> = ({ blogPostData }) => (
    <div>
      {/* dyanmic head for seo */}
      <Head>
        <title>{blogPostData.title} | Andrew Shearer Dev Portfolio</title>
        <meta name='description' content={blogPostData.title} />
      </Head>

      <h1>Blog Post Page</h1>

      <p>
        This is the blog post page for <strong>{blogPostData.title}</strong>
      </p>
    </div>
  );

  export default BlogPostPage;
  ```

  - You can see we also use the `<Head />` component from Next to dynamically set the title and meta description of the page for SEO purposes

## Typing the `getStatic` Functions

Now let's update the types for the 2 `getStatic` functions

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

## Display the Markdown Content

We can use the [`react-markdown`](https://www.npmjs.com/package/react-markdown) package to easily render the Markdown text from our Blog Posts.

_**NOTE**_: Another solid choice would be [`@contentful/rich-text-react-renderer`](https://www.npmjs.com/package/@contentful/rich-text-react-renderer). However, at the time of writing (Apr 16, 2022), I was using React v18, and `@contentful/rich-text-react-renderer` is only working with React v16 & v17.

We currently have this in the JSX in `pages/blog/[slug].tsx`:

```ts
<p className='markdown'>
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Cupiditate, cumque.
</p>
```

First, install `react-markdown`: `npm i react-markdown`

If you change the `<p>` tag to this:

```ts
<p className='markdown'>{blogPostData.content}</p>
```

It will look like one giant big mess!

First, import react-markdown:

```ts
import ReactMarkdown from 'react-markdown';
```

Then simply pass the `content` as children to the `ReactMarkdown` component:

```ts
<ReactMarkdown>{blogPostData.content}</ReactMarkdown>
```

Our list styling reset in GlobalStyle.ts is not rendering lists and links correctly. You could create a `StyledReactMarkdown` and use that instead. It would look something like this:

```ts
import styled from 'styled-components';

const StyledReactMarkdown = styled(ReactMarkdown)`
  ul {
    list-style: disc;
    padding-left: 1.5rem;
  }

  a {
    color: blue;
  }
`;

// down in the jsx...
<StyledReactMarkdown>{blogPostData.content}</StyledReactMarkdown>;
```

## GraphQL Fragments

We can use [fragments](https://graphql.org/learn/queries/#fragments) to group together highly reused fields and keep our query's lean and mean.

- Create a `graphql` folder and then a file `fragments.ts` in that folder:
  - `mkdir graphqlol && cd graphqlol && touch fragments.ts && cd ..`
- In this file, add the following:

```ts
import { gql } from '../utils/gql';

export const FRAGMENT_CONTENTFUL_IMAGE = gql`
  fragment ImageFields on Asset {
    description
    height
    sys {
      id
    }
    url
    width
  }
`;
```

- Next, let's go to `pages/index.tsx`

  - Since we are using template strings, we can interpolate the fragment. Fragments should be place outside the closing pair query `{ }` brackets:

  ```ts
  // .. rest of fetch call up here ^^

  body: JSON.stringify({
    query: gql`
      query HomepageQuery {
        blogPostCollection {
          items {
            image {
              ...ImageFields
            }
            slug
            sys {
              id
            }
            title
          }
        }
      }

      ${FRAGMENT_CONTENTFUL_IMAGE}
    `
  });
  ```

  - Then simply replace all the fields the fragment uses with `...ImageFields`
  - We can now update the query in `pages/blog/[slug].tsx` to use this fragment as well:

  ```ts
  body: JSON.stringify({
    query: gql`
      query SingleBlogPostQuery($slug: String!) {
        blogPostCollection(where: { slug: $slug }, limit: 1) {
          items {
            author {
              bio
              image {
                ...ImageFields
              }
              name
            }
            content
            image {
              ...ImageFields
            }
            title
          }
        }
      }

      ${FRAGMENT_CONTENTFUL_IMAGE}
    `,
    variables: {
      slug
    }
  });
  ```

- Now the query code is a bit cleaner and easier to read!

## Update Styling

Our app looks pretty awful right now. Let's add some basic styles to spruce it up a bit.

- To help keep our components organized, we'll create folders for each one: `cd components && mkdir BlogPosts Footer Layout Nav && cd ..`
- Then move each component into it's own folder
- Next, create a styles file in each component folder like so: `[ComponentName].styles.ts`
  - This styles file will house any Styled Components that are used by ONLY the component in the same folder
  - Any global Styled Components will get put in a `UI` folder in the components folder: `cd components && mkdir UI && cd ..`
- Next, let's add a basic colour palette and font sizes we can use throughout. Let's update our `theme.ts`

  ```ts
  const theme = {
    // ...
    colors: {
      // color palette from:
      // https://colorhunt.co/palette/06113cff8c32ddddddeeeeee

      sapphire: '#06113c', // dark blue
      carrot: '#ff8c32', // orange
      gainsboro: '#ddd', // light grey
      whiteSmoke: '#eee', // off-white grey
      nero: '#141414' // dark grey
    },
    fontSizes: {
      copy: {
        small: '0.875rem',
        medium: '1rem',
        large: '1.125rem'
      },
      heading: {
        small: 'clamp(1.25rem, 5vw, 2rem)',
        medium: 'clamp(1.75rem, 5vw, 3.25rem)',
        large: 'clamp(2.5rem, 5vw, 4.5rem)'
      }
    }
    // ...
  };
  ```

- Now let's update our components

  - `Footer`

    - Styles:

    ```ts
    export const Footer = styled.footer`
      background-color: ${({ theme }) => theme.colors.sapphire};
      color: ${({ theme }) => theme.colors.whiteSmoke};
      font-size: 1.25rem;
      padding: 2rem;
      text-align: center;

      p {
        font-size: inherit;
      }
    `;
    ```

    - Component:

    ```ts
    // styled components
    import * as S from './Footer.styles';

    const Footer = () => (
      <S.Footer>
        <p>
          &copy; {new Date().getFullYear()} all rights reserved <span> | </span> designed and built
          by andrew shearer
        </p>
      </S.Footer>
    );
    ```

  - `Nav`

    - Styles:

    ```ts
    export const Header = styled.header`
      background-color: ${({ theme }) => theme.colors.gainsboro};
      color: ${({ theme }) => theme.colors.nero};
      font-size: ${({ theme }) => theme.fontSizes.heading.small};
      font-weight: ${({ theme }) => theme.fontWeights.bold};
      padding: 1.5% 5%;
    `;
    ```

    - Component:

    ```ts
    // styled components
    import * as S from './Nav.styles';

    const Nav = () => (
      <S.Header>
        <nav className='nav'>
          <ul>
            <li>
              <Link href='/'>Next.js Contentful Blog</Link>
            </li>
          </ul>
        </nav>
      </S.Header>
    );
    ```

  - `BlogPosts`

    - Styles:

    ```ts
    import styled from 'styled-components';

    export const Wrapper = styled.div`
      margin: 0 auto;
      padding: 5% 0;
      width: 90%;
    `;

    export const Grid = styled.div`
      display: grid;
      grid-gap: 3.5vw;
      grid-template-columns: repeat(12, 1fr);

      @media ${({ theme }) => theme.mediaQueries.tabletPortrait} {
        grid-template-columns: repeat(4, 1fr);
        grid-gap: 7vw;
      }
    `;

    export const Card = styled.div`
      border-radius: 1.5rem;
      grid-column: span 4;
      overflow: hidden;
      transition: ${({ theme }) => theme.transitions.short};

      @media (hover) {
        &:hover {
          box-shadow: 0 0.75rem 1.5rem 0 rgba(0, 0, 0, 0.1);
          transform: translateY(-0.25rem);
        }
      }

      @media ${({ theme }) => theme.mediaQueries.tabletLandscape} {
        grid-column: span 6;
      }

      @media ${({ theme }) => theme.mediaQueries.tabletPortrait} {
        grid-column: 1 / -1;
      }
    `;

    export const CardBody = styled.div`
      background-color: ${({ theme }) => theme.colors.whiteSmoke};
      margin-top: -4px;
      padding: 1.5rem;

      a {
        font-weight: ${({ theme }) => theme.fontWeights.bold};

        @media (hover) {
          &:hover,
          &:active,
          &:focus {
            color: ${({ theme }) => theme.colors.carrot};
          }
        }
      }
    `;

    export const PostTitle = styled.h2`
      font-size: ${({ theme }) => theme.fontSizes.copy.large};
      font-weight: ${({ theme }) => theme.fontWeights.normal};
      margin-bottom: 1rem;
    `;
    ```

    - Component:

    ```ts
    const BlogPosts = ({ posts }: BlogPostsProps) => (
      <S.Wrapper>
        <S.Grid>
          {posts.map((post) => (
            <S.Card key={post.sys.id}>
              <Image
                src={post.image.url}
                alt={post.image.description}
                height={post.image.height}
                width={post.image.width}
                placeholder='blur'
                blurDataURL={post.image.url}
              />

              <S.CardBody>
                <S.PostTitle>{post.title}</S.PostTitle>

                <Link href={`/blog/${post.slug}`}>Read Post &rarr;</Link>
              </S.CardBody>
            </S.Card>
          ))}
        </S.Grid>
      </S.Wrapper>
    );
    ```

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

- Finally, the individual Blog Post page:

  - Styles:

  ```ts
  import styled from 'styled-components';
  import ReactMarkdown from 'react-markdown';

  export const Wrapper = styled.div`
    margin: 0 auto;
    padding: 5% 0;
    width: 90%;
  `;

  export const PostTitle = styled.h1`
    font-size: ${({ theme }) => theme.fontSizes.heading.large};
    font-weight: ${({ theme }) => theme.fontWeights.normal};
  `;

  export const ImageWrapper = styled.div`
    height: 33vw;
    margin: 2rem 0;
    overflow: hidden;

    & > span {
      height: 100%;

      img {
        object-fit: cover;
        object-position: center;
      }
    }
  `;

  export const StyledReactMarkdown = styled(ReactMarkdown)`
    /* add spacing to all these elements */
    h1,
    h2,
    h3,
    h4,
    h5,
    h6,
    ol,
    ul,
    hr,
    pre {
      margin: 0.75rem 0;
    }

    /* headings */
    h1 {
      font-size: 2rem;
    }

    h2 {
      font-size: 1.75rem;
    }

    h3 {
      font-size: 1.5rem;
    }

    h4 {
      font-size: 1.375rem;
    }

    h5 {
      font-size: 1.25rem;
    }

    h6 {
      font-size: 1.125rem;
    }

    /* lists */
    ol,
    ul {
      padding-left: 1.25rem;
    }

    ul {
      list-style: disc;
    }

    ol {
      list-style: decimal;
    }

    /* links */
    a {
      color: ${({ theme }) => theme.colors.carrot};
    }

    /* code blocks */
    pre {
      background-color: #ddd;
      line-height: 1.5;
      padding: 0.75rem;
    }
  `;
  ```

  - Component:

  ```ts
  const BlogPostPage: NextPage<BlogPostPageProps> = ({ blogPostData }) => (
    <>
      {/* dyanmic head for seo */}
      <Head>
        <title>{blogPostData.title} | Andrew Shearer Dev Portfolio</title>
        <meta name='description' content={blogPostData.title} />
      </Head>

      <S.Wrapper>
        <S.PostTitle>{blogPostData.title}</S.PostTitle>

        <S.ImageWrapper>
          <Image
            src={blogPostData.image.url}
            alt={blogPostData.image.description}
            height={blogPostData.image.height}
            width={blogPostData.image.width}
            placeholder='blur'
            blurDataURL={blogPostData.image.url}
          />
        </S.ImageWrapper>

        <S.StyledReactMarkdown>{blogPostData.content}</S.StyledReactMarkdown>
      </S.Wrapper>
    </>
  );
  ```

## Displaying the Author

Now let's update the blog post template to display the author as well.

- Create a new `Author` folder in the components folder, and create `Author.tsx` & `Author.styles.ts` as well:

`cd components && mkdir Author && cd Author && touch Author.tsx Author.styles.ts && cd .. && cd ..`

- Styles:

```ts
import styled from 'styled-components';

export const Wrapper = styled.div`
  display: flex;
  align-items: center;
  gap: 1.5rem;
  border-top: 1px solid ${({ theme }) => theme.colors.nero};
  margin-top: 2rem;
  padding-top: 2rem;

  @media ${({ theme }) => theme.mediaQueries.tabletPortrait} {
    flex-direction: column;
    text-align: center;
  }
`;

export const ImageWrapper = styled.div`
  height: 8rem;
  width: 8rem;
  border-radius: 50%;
  overflow: hidden;

  & > span {
    height: 100% !important; /* need important to override default styles */

    img {
      object-fit: cover;
      object-position: center;
    }
  }
`;

export const TextWrapper = styled.div`
  h3 {
    font-size: ${({ theme }) => theme.fontSizes.heading.small};
  }
`;
```

- Component:

```ts
// props type
type AuthorProps = {
  authorData: ContentfulAuthor;
};

const Author = ({ authorData }: AuthorProps) => (
  <S.Wrapper>
    <S.ImageWrapper>
      <Image
        src={authorData.image.url}
        alt={authorData.image.description}
        height={authorData.image.height}
        width={authorData.image.width}
        placeholder='blur'
        blurDataURL={authorData.image.url}
      />
    </S.ImageWrapper>

    <S.TextWrapper className='text-wrapper'>
      <h3>{authorData.name}</h3>
      <p>{authorData.bio}</p>
    </S.TextWrapper>
  </S.Wrapper>
);
```

- Update `BlogPostPage` to display the `Author` component below the `StyledReactMarkdown`:

```tsx
const BlogPostPage: NextPage<BlogPostPageProps> = ({ blogPostData }) => (
  <>
    ...

      <S.StyledReactMarkdown>{blogPostData.content}</S.StyledReactMarkdown>

      {/* AUTHOR GOES HERE */}
      <Author authorData={blogPostData.author} />
    </S.Wrapper>
  </>
);
```

## Creating Custom `NextImage` Component

Before we add Categories and RelatedPosts, it will be good idea to create our own custom `NextImage` component which uses the `Image` component.

We're already using it in a few places with the same props, so we should create one component with it all pre-configured.

- Create a new `NextImage` folder in the components folder, and create `NextImage.tsx`:

`cd components && mkdir NextImage && cd NextImage && touch NextImage.tsx && cd .. && cd ..`

- Component Code:

```ts
import Image from 'next/image';

// custom types
import { ContentfulImage } from '../../types/contentful';

type NextImageProps = {
  imageData: ContentfulImage;
};

const NextImage = ({ imageData }: NextImageProps) => (
  <Image
    src={imageData.url}
    alt={imageData.description}
    height={imageData.height}
    width={imageData.width}
    placeholder='blur'
    blurDataURL={imageData.url}
  />
);

export default NextImage;
```

Now replace instances of using `Image` with `NextImage` in `Author.tsx`, `BlogPosts.tsx`, & `blog/[slug].tsx`.

You will pass down the entire `.image` object in each case.

## Adding Date Published and Time To Read

Let's update our BlogPost Model to have a Date Published, as well as display the estimated Time to Read.

We'll place both of these pieces of information right below the Image on each individual post page.

### Date Published

- In Contentful, go to the Blog Post model
  - Add a new `Date & time Field` and call it `Date Published`
  - Set it to be Required, and click Confirm
  - Then click Save
- Go to each Blog Post piece of content and add a Date Published
  - Make sure to give them each a different date so we can sort them when we query them
- In `pages/index.tsx`, update the query to sort the Posts by Date Published in descending order

  - Before:

  ```ts
  const gql = String.raw;

  query HomepageQuery {
    blogPostCollection {
      # ...
    }
  }
  ```

  - After:

  ```ts
  const gql = String.raw;

  query HomepageQuery {
    blogPostCollection(order: [datePublished_DESC]) {
      # ...
    }
  }
  ```

- Make sure to update the `ContentfulBlogPost` type as well:

```ts
export type ContentfulBlogPost = {
  author: ContentfulAuthor;
  content: string;
  datePublished: string; // DATE ADDED!
  image: ContentfulImage;
  slug: string;
  sys: {
    id: string;
  };
  title: string;
};
```

We'll need to create a formatDate function to correctly format the ISO 8601 Date String returned by Contentful.

- Create a file formatDate.ts inside the utils folder: `cd utils && touch formatDate.ts && cd ..`
- In this file, add the following:

```ts
const months = [
  'January',
  'February',
  'March',
  'April',
  'May',
  'June',
  'July',
  'August',
  'September',
  'October',
  'November',
  'December'
];

export const formatDate = (dateStr: string) => {
  // starting date
  const date = new Date(dateStr);

  // construct desired pieces of data
  const month = months[date.getMonth() + 1];
  const day = date.getDate();
  const year = date.getFullYear();

  // return formatted date
  return `${month} ${day}, ${year}`;
};
```

- Back in the page component file, add the following below the image:

```ts
<div className='info-wrapper'>
  <p className='date'>
    <strong>Date Published: </strong>
    <span>{formatDate(blogPostData.datePublished)}</span>
  </p>
  <p className='time-to-read'>
    <strong>Estimated Time to Read: </strong>
    <span>min</span>
  </p>
</div>
```

Next we'll add the estimated Time to Read

### Time to Read

- For this, we'll need a couple of things:
  - Average words per minute read (~225)
  - The total number of words in the post `content`
- We can create another util function called `timeToRead`: `cd utils && touch timeToRead.ts && cd ..`

  - In this file, add the following:

  ```ts
  export const timeToRead = (content: string) => {
    const wordsPerMinute = 225;
    const numberOfWordsInPost = content.trim().split(/\s+/).length;

    return Math.ceil(numberOfWordsInPost / wordsPerMinute);
  };
  ```

- Back in the page component file, update this section to use the function:

```ts
<div className='info-wrapper'>
  // ...
  <p className='time-to-read'>
    <strong>Estimated Time to Read: </strong>
    <span>{timeToRead(blogPostData.content)} min</span>
  </p>
</div>
```

- Now let's create a Styled Component to display these 2 pieces of information side-by-side and then stack them on mobile:

```ts
export const InfoWrapper = styled.div`
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 2rem;

  @media ${({ theme }) => theme.mediaQueries.tabletPortrait} {
    flex-direction: column;
    gap: 0.5rem;
  }
`;
```

- Update the JSX to this (add a bullet and remove unnecessary class names:)

```ts
<S.InfoWrapper>
  <p>
    <strong>Date Published: </strong>
    <span>{formatDate(blogPostData.datePublished)}</span>
  </p>

  <strong>&bull;</strong>

  <p>
    <strong>Estimated Time to Read: </strong>
    <span>{timeToRead(blogPostData.content)} min</span>
  </p>
</S.InfoWrapper>
```

- Let's also add `timeToRead` to the cards in `BlogPosts.tsx` (yes this is redundant, you can choose where you want to place it)

  - Update query in `pages/index.tsx` to query for `content`

  - Create Styled Component in `BlogPosts.styles.ts`:

  ```ts
  export const TimeToRead = styled.span`
    display: block;
    font-size: ${({ theme }) => theme.fontSizes.copy.small};
    margin-top: 2rem;
  `;
  ```

  - Update the JSX:

  ```ts
  <S.CardBody>
    <S.PostTitle>{post.title}</S.PostTitle>

    <Link href={`/blog/${post.slug}`}>Read Post &rarr;</Link>

    {/* ADD TIME TO READ HERE */}
    <S.TimeToRead>{timeToRead(post.content)} min read</S.TimeToRead>
  </S.CardBody>
  ```

## Adding Preview Text

We'll also now add some preview text to each Blog post. This text will appear on each card.

We'll also take this time to update each card's layout.

- In Contentful, select the Blog Post model

  - Add a Text field
    - Select Long Text
    - Give it a name
    - Make it required
    - Set style to multi line
  - Go to each Blog Post and add in some preview text. You can use this dummy lorem ipsum text if you wish:

    - "_This post is about lorem, ipsum dolor sit amet consectetur adipisicing elit. Ipsa, dicta._"

- In the Code

  - Update the `ContentfulBlogPost` type:

  ```ts
  export type ContentfulBlogPost = {
    author: ContentfulAuthor;
    content: string;
    datePublished: string;
    image: ContentfulImage;
    previewText: string; // NEW
    slug: string;
    sys: {
      id: string;
    };
    title: string;
  };
  ```

  - Update the query in `pages/index.tsx`:

  ```ts
    query HomepageQuery {
      blogPostCollection(order: [datePublished_DESC]) {
        items {
          content
          image {
            ...ImageFields
          }
          previewText
          slug
          sys {
            id
          }
          title
        }
      }
    }
  ```

  - Update the JSX in `BlogPosts.tsx`:

  ```ts
  <S.CardBody>
    <S.PostTitle>
      {post.title}
      <span>&bull;</span>
      <S.TimeToRead>{timeToRead(post.content)} min read</S.TimeToRead>
    </S.PostTitle>

    <S.PreviewText>{post.previewText}</S.PreviewText>

    <Link href={`/blog/${post.slug}`}>Read Post &rarr;</Link>
  </S.CardBody>
  ```

  - Updated Styled Components:

  ```ts
  export const PostTitle = styled.h2`
    display: flex;
    align-items: center;
    font-size: ${({ theme }) => theme.fontSizes.copy.xlarge};
    font-weight: ${({ theme }) => theme.fontWeights.normal};
    gap: 0.75rem;
  `;

  export const PreviewText = styled.p`
    font-size: ${({ theme }) => theme.fontSizes.copy.medium};
    margin: 1.25rem 0;
  `;

  export const TimeToRead = styled.span`
    font-size: ${({ theme }) => theme.fontSizes.copy.small};
  `;
  ```

  - Added new font size in the theme:

  ```ts
  const theme = {
    fontSizes: {
      copy: {
        small: '0.875rem',
        medium: '1rem',
        large: '1.125rem',
        xlarge: '1.25rem' // NEW FONT SIZE
      }
    }
  };
  ```

## Adding Categories

Blog typically come with Categories. And with those Categories, we want to be able to:

- Add Categories to each post
- Click on a Category and be taken to page showing all posts using that category
- Show Related Posts at the bottom of a post with all the same categories

### Adding Categories to Each Post

- In Contentful, create a new `Category` Content Model. It will have the following fields:
  - `Name` (Entry Title, and is Unique)
  - `Slug` (Short text, based off of Entry Title)
- Create a few Categories, around 8-10 or so, and name them whatever you like.
  - To keep things simple, I will use `CategoryA`, `CategoryB`, `CategoryC`, etc. up to `CategoryH`
- Next, go to the Blog Post Model and add a reference field called `Categories`
  - Select `Many references`
  - Accept only 1-4 Categories (similar to [dev.to](https://dev.to/))
  - Accept only `Category` entry type
  - Make it required
- Now go to each Blog Post and randomly add 1 - 4 Categories, just making sure you add all of them at least once
  - You can go back to the Contentful GraphiQL playground and run a query to make sure:
  ```ts
  query CategoriesTestQuery {
    blogPostCollection {
      items {
        categoriesCollection {
          items {
            sys {
              id
            }
            name
            slug
          }
        }
        title
      }
    }
  }
  ```
- Next we need to update our types in `types/contentful.ts`:

```ts
// reusable
export type ContentfulId = {
  id: string;
};

export type ContentfulImage = {
  description: string;
  height: number;
  sys: ContentfulId;
  url: string;
  width: number;
};

// sections
export type ContentfulAuthor = {
  bio: string;
  image: ContentfulImage;
  name: string;
};

export type ContentfulCategory = {
  sys: ContentfulId;
  name: string;
  slug: string;
};

export type ContentfulBlogPost = {
  author: ContentfulAuthor;
  categoriesCollection: {
    items: ContentfulCategory[];
  };
  content: string;
  datePublished: string;
  image: ContentfulImage;
  previewText: string;
  slug: string;
  sys: ContentfulId;
  title: string;
};
```

- Also created a reusable `ContentfulId` type since that structure appears a few times
- Next, we'll simply add each posts' categories to their card to make sure it'll display correctly

  - Update the query in `pages/index.tsx`:

  ```ts
  query HomepageQuery {
    blogPostCollection(order: [datePublished_DESC]) {
      items {
        categoriesCollection {
          items {
            sys {
              id
            }
            name
            slug
          }
        }
        content
        image {
          ...ImageFields
        }
        previewText
        slug
        sys {
          id
        }
        title
      }
    }
  }
  ```

  - Add this JSX to the `Card` below the `PreviewText`:

  ```ts
  <ul>
    {post.categoriesCollection.items.map((category) => (
      <li key={category.sys.id}>{category.name}</li>
    ))}
  </ul>
  ```

### Category Routing

Now we want to create dynamic routes for the categories just like we did with the posts.

When a user clicks on a Category, they'll go to a page that'll display all the posts that have that category.

- In the `pages` folder, create a new folder `category`, and then in there a new file `[slug].tsx`:

`cd pages && mkdir category && cd category && touch [slug].tsx && cd .. && cd ..`

- In this `[slug].tsx` file, add the following boilerplate code (there'll be a lot of TypeScript / ESLint errors & warnings, but we'll address them shortly):

```ts
import type { GetStaticPaths, GetStaticProps } from 'next';

export const getStaticPaths: GetStaticPaths = async () => {
  return {
    paths: slugs,
    fallback: false // show 404 page
  };
};

export const getStaticProps: GetStaticProps = async ({ params }) => {
  return {
    props: {}
  };
};

type CategoryPageProps = {};

const CategoryPage = (props: CategoryPageProps) => {
  return (
    <div>
      <h1>Category Page</h1>
    </div>
  );
};

export default CategoryPage;
```

- We can copy & paste the internal code from `getStaticPaths` inside the `pages/blog/[slug].tsx` file and make some adjustments for categories:

```ts
type PathsGraphQLResponse = {
  data: {
    categoryCollection: {
      items: ContentfulCategory[];
    };
  };
};

export const getStaticPaths: GetStaticPaths = async () => {
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
          query CategorySlugsQuery {
            categoryCollection {
              items {
                slug
              }
            }
          }
        `
      })
    }
  );

  const { data }: PathsGraphQLResponse = await response.json();

  const slugs = data.categoryCollection.items.map((category) => ({
    params: { slug: category.slug }
  }));

  return {
    paths: slugs,
    fallback: false // show 404 page
  };
};
```

- We can actually update the `BlogPosts` card to link to these pages now:

```ts
<ul className='categories-list'>
  {post.categoriesCollection.items.map((category) => (
    <li key={category.sys.id}>
      <Link href={`/category/${category.slug}`}>{category.name}</Link>
    </li>
  ))}
</ul>
```

- We will only see the plain `h1` _"Category Page"_, but it's a start!

We will need the `IParams` type like we created for the blog post dynamic route, so let's add it to `types/global.ts` (create this file):

```ts
import type { ParsedUrlQuery } from 'querystring';

export type IParams = ParsedUrlQuery & {
  slug: string;
};
```

- Now we can re-use this type again if we create dynamic routes for other pieces of content

When it comes to querying posts by their category, that's where things get tricky. Ideally, we would do something like this:

```ts
query SingleBlogPostQuery($slug: String!) {
  blogPostCollection(order: [datePublished_DESC]) {
    items {
      categoriesCollection(where: {slug: $slug}) {
        items {
          sys {
            id
          }
          name
          slug
        }
      }
      content
      image {
        ...ImageFields
      }
      previewText
      slug
      sys {
        id
      }
      title
    }
  }
}
```

Where we filter posts by the nested `categoriesCollection`. However, this is not possible because categoriesCollection is an object, and the `where` filter can only be used on arrays. So, what do we do here?

Instead, we want to query for all `categories` of the matching slug, then find any BlogPosts that are linked to that matching category.

The query looks like this:

```ts
body: JSON.stringify({
  query: gql`
    query PostByCategoryQuery($slug: String!) {
      categoryCollection(where: { slug: $slug }) {
        items {
          linkedFrom {
            blogPostCollection {
              items {
                previewText
                slug
                sys {
                  id
                }
                title
              }
            }
          }
          name
        }
      }
    }
  `,
  variables: {
    slug
  }
});
```

The type for this also looks pretty crazy:

```ts
type PropsGraphQLResponse = {
  data: {
    categoryCollection: {
      items: {
        linkedFrom: {
          blogPostCollection: {
            items: ContentfulBlogPost[];
          };
        };
        name: string;
      }[];
    };
  };
};
```

So, the `getStaticPaths()` looks like this in the end:

```ts
export const getStaticProps: GetStaticProps = async ({ params }) => {
  // get contentful data
  const { slug } = params as IParams;

  // get contentful data
  const response = await fetch();
  // ... query here

  const { data }: PropsGraphQLResponse = await response.json();

  const blogPostData = data.categoryCollection.items;

  return {
    props: {
      blogPostData
    }
  };
};
```

Now we can setup the type for the `blogPostData` prop:

```ts
type CategoryPageProps = {
  blogPostData: {
    linkedFrom: {
      blogPostCollection: {
        items: ContentfulBlogPost[];
      };
    };
    name: string;
  }[];
};
```

We can update the component to this:

```ts
const CategoryPage: NextPage<CategoryPageProps> = ({ blogPostData }) => {
  console.log('blogPostData: ', blogPostData);

  return (
    // some jsx here
  );
};
```

When we check the `console.log()` to view `blogPostData`, we can see there is an array with a single object in it. Like we did before, we can destructure this one object out of the array like so:

```ts
const [data] = blogPostData;
```

Now we can access `data.name`, and `data.linkedFrom` much more easily.

We'll display a basic heading mentioning which `category` you are viewing, and display just the post `title` for now:

```ts
const CategoryPage: NextPage<CategoryPageProps> = ({ blogPostData }) => {
  // destructure the one item out of blogPostData array
  const [data] = blogPostData;

  return (
    <>
      {/* dyanmic head for seo */}
      <Head>
        <title>{data.name} | Next.js Contentful Blog</title>
        <meta name='description' content={data.name} />
      </Head>

      <h1>Category Page for {data.name}</h1>

      <div className='grid'>
        {data.linkedFrom.blogPostCollection.items.map((post) => (
          <h3 key={post.title}>{post.title}</h3>
        ))}
      </div>
    </>
  );
};
```

You may have noticed that our query for the BlogPosts is a lot smaller than on the home page. This is due to [GraphQL Query Complexity Limits](https://www.contentful.com/developers/docs/references/graphql/#/introduction/query-complexity-limits). We can only query for those fields. So, we need to create a simpler version of how the BlogPost cards are displayed.

This means first, we'll want to abstract the MainHeading styled component into a reusable global UI component:

```ts
// components/UI/MainHeading.ts

import styled from 'styled-components';

export const MainHeading = styled.h1`
  font-size: ${({ theme }) => theme.fontSizes.heading.large};
  margin: 3.5% 0 0 5%;
`;
```

## Creating & Displaying Related Posts

Most blogs have a Related Posts or similarly named section underneath the post.

We can leverage our Categories to only show related posts with the same categories.

- In `pages/blog/[slug].tsx`

## Host on Vercel

- Go to [Vercel](https://vercel.com/) and create an account if you don't already have one
- Then go to your [dashboard](https://vercel.com/dashboard)
- Click `+ New Project`
- Select the project from GitHub (authorize if needed)
- Enter the environment variables one at a time
- Click `Deploy`
  - _**NOTE**_: If you have any errors of any kind (warnings ok), the site will not build
