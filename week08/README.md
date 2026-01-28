# Week 8 Refresher Guide

<details><summary><h3>Next.js Intro</h3></summary>

- Next.js is a popular framework that builds on React
  - React is great for building nice UIs but it doesn't include some of the features we need to build full stack apps without adding third-party libraries
- The key difference is that Next.js uses SSR (Server Side Rendering) which means all of the JavaScript used to generate the HTML is run on the server and the completed HTML document is returned all at once, keeping the amount of JavaScript that gets sent to the client so the page is downloaded faster
  - This is in contrast to React (and vanilla JS) where the code is run on the client and the page is created as it runs
- Another key feature of Next.js is that it includes routing similar to what React Router allows us to do
- We still have components and props like in React but instead of a single `App.jsx` file we have multiple `page.js` files, one for each page
</details>

<details><summary><h3>Setting Up a Next Project</h3></summary>

- To create a Next.js project we run `npx create-next-app@latest`
  - This will give us some questions to configure the app, here's what we want to use for now:
    Would you like to use the recommended Next.js defaults? › No, customize settings  
    Would you like to use TypeScript? … No  
    Which linter would you like to use? › ESLint  
    Would you like to use React Compiler? … No  
    Would you like to use Tailwind CSS? … Yes  
    Would you like your code inside a `src/` directory? … Yes  
    Would you like to use App Router? (recommended) … Yes  
    Would you like to customize the import alias (`@/*` by default)? … No  
  - After choosing these setting the first time you should be able to select `No, reuse previous settings` for the first question 
  - It might seem obvious to choose the default settings but this would create the project with TypeScript instead of standard JavaScript which we don't want at this stage
- Running the Next app is similar to what we've done in React
  - Just `cd` into the directory as usual and run `code .` to open it
  - We can skip `npm install` because `create-next-app` already installs the dependencies for us
  - Just like React we run `npm run dev` to start the dev server, this time on localhost:3000
- The other key commands for Next.js are `build` and `start`
  - `npm run build` will build a production version of the app, just like Vite did for us with React and vanilla
  - `npm run start` will start a production server on port 3000 but only after we run `build`
</details>

<details><summary><h3>File Routing</h3></summary>

- The structure of a default Next app has some important differences to a React app
- The key thing here is that Next has file-based routing which means we can navigate to multiple pages without having to add anything like React Router's `Route` and `BrowserRouter` components
- All of our code goes in the `/src` directory but we also have an `/app` directory that contains all of our routes
  - Each directory within `/app` represents a different route available on the site, with a `page.js` file inside it containing the JSX for the page that is on that route
    - We have a `page.js` that is directly within `/app` which is our home page on the root route
    - A `page.js` inside an `/about-us` page, for example, will be loaded when we go to `www.example.com/about-us`
- We also have a `layout.js` file which contains the JSX to be loaded on every page, with the `children` prop being substituted for the content of each page
  - The React Router equivalent would be everything in `App.jsx` that's outside of the `Routes` component so it
  - `layout.js` contains the `<html>` and `<body>` tags by default but some of the most common things to add to it are a header and footer that we want on every page
- We can create dynamic routes here too by creating a directory with square brackets e.g. `/app/posts/[id]`
  - We can access the params in the page by using the `params` prop
    ```js
    {/* /posts/[id]/page.js */}

    export default async function PostPage({ params }) {
      const pageParams = await params;

      return(
        <p>This post has an id of: {pageParams.id}</p>
      );
    }
    ```
    - The `params` prop is a promise which is why we need to await it and make the component function asynchronous
      - We can get specific values in one line if we wrap it in brackets like `const id = (await params).id`
    - Awaiting params returns an object with each of the params as properties with names matching what we put in square brackets in the directory name so in this case it's just id
      - Most of the time we'll only have one param so only one property in the object but in cases with nested dynamic routes we would have multiple params
- Next.js comes with many components we can import and one of the most common is `<Link>`
  - This is used to link to other pages similarly to the `Link` component in React Router
  - Like all components we need to import it to use it
    ```js
    import Link from "next/link";

    export default function NavBar() {
      return(
        <>
          <nav>
            <Link href="/about">About Us</Link>
            <Link href="/feed">Feed</Link>
            <Link href="/profile">Profile</Link>
          </nav>
        </>
      )
    }
    ```
  - One important thing to remember when switching from React Router to Next is that `Link` in Next has a `href` prop instead of a `to` prop
- We can also link to dynamic routes the same way as long as we include the params in the href
  ```js
  import Link from "next/link";

  export default function Posts({ postArray }) {
    return(
      <>
        {postArray.map((post) => (
          <Link href=`/post/${post.id}` key={post.id}>
            <div>
              <h1>{post.title}</h1>
              <p>{post.content}</p>
            </div>
          </Link>
        ))}
      </>
    )
  }
  ```
  - Most of the time we need to use a template literal so we can vary the params
</details>

<details><summary><h3>Metadata</h3></summary>

- Standard HTML uses the `<title>` and `<meta>` tags to create our metadata but these go in the `<head>` which isn't easily accessible in Next.js or React
- Next has a way around this though, letting us create a metadata object that gets rendered in `<head>` when the page is created
- We can create a default metadata object in `layout.js` for the whole app then override it on specific pages in each `page.js`
  ```js
  {/* layout.js */}

  export const metadata = {
    title: "My Lovely Site",
    description: "This is a lovely website that has some metadata!",
  }
  ```
  ```js
  {/* /posts/page.js */}

  export const metadata = {
    title: "My Lovely Site - Posts",
    description: "This page shows all the posts that users have made",
  }
  ```
- We can add go further by adding Open Graph information to our metadata as an object
  - Open Graph determines how it looks when a link is shared on social media or apps like Discord, we've all seen it before without realising
  ```js
  {/* layout.js */}

  export const metadata = {
    title: "My Lovely Site",
    description: "This is a lovely website that has some metadata!",
    openGraph: {
      title: "My Lovely Site",
      description: "This is a lovely website that has some metadata!",
      type: "website",
      url: "https://www.mylovelysite.com",
    },
  }
  ```
  - We can add images to enhance the appearance of our Open Graph links but we don't do it in the metadata object, we have to add an image file in the same directory as the page called opengraph-image (with the correct file extension)
    ```
    /src
    |  /app
    |  |  /posts
    |  |  |  opengraph-image.jpg
    |  |  |  opengraph-image.alt.txt
    |  |  |  page.js
    |  |  layout.js
    |  |  opengraph-image.png
    |  |  opengraph-image.alt.txt
    |  |  page.js
    ```
    - To add alt text to these images we can create another file called opengraph-image.alt.txt containing our alt text
- If we have a dynamic page then we also want our metadata to be dynamic so that it can, for example, display a post title as the page title
  - Instead of exporting a metadata object we export the `generateMetadata` function where we return an object, usually after fetching some data
    ```js
    {/* /post/[id]/page.js */}

    export async function generateMetadata({ params }) {
      const id = (await params.id);
      const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
      const post = await res.json();
      return {
        title: post.title,
        description: post.body,
      };
    }
    ```
    - Because this function is outside the component function, we would need to fetch the data again inside the component function if we want to use it there as well as in the metadata
</details>

<details><summary><h3>Data Fetching in Next</h3></summary>

- One of the benefits of our components running on the server is that we don't need to make any API routes for fetching data, we can make the API calls directly from our components
- Since our components are run on the server in Next the data will be fetched when the component runs without any need for `useEffect` either
- Using the JSON Placeholder API as an example once again, we can fetch data directly from there in our server components
  ```js
  {/* /posts/page.js */}

  export async function PostsPage() {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
    const posts = await res.json();

    return(
      <>
        {posts.map((post) => (
          <div key={post.id}>
            <h2>{post.title}</h1>
            <p>{post.body}</p>
          </div>
        ))}
      </>
    );
  }
  ```
  - The process here is the same as what we did in vanilla JS (and React minus the `useEffect`) with fetching the data then parsing it from JSON to a usable object
  - Our component functions need to be asynchronous if we're awaiting anything within them
- If we're fetching from a dynamic route then we can use the params from our own URL in the fetch URL
  ```js
  {/* /posts/[id]/page.js */}

  export async function IndividualPost({ params }) {
    const id = (await params).id;
    const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
    const post = await res.json();

    return(
      <div>
        <h2>{post.title}</h1>
        <p>{post.body}</p>
      </div>
    );
  }
  ```
  - A common setup in Next.js is a 'posts' page that fetches all posts, and a dynamic 'post' page that fetches a single post dependent on the params
- We can use query strings in Next though they are technically called `searchParams` here
  - This is handy for filtering or sorting data that we fetch
  ```js
  {/* /posts/page.js */}

  export async function PostsPage({ searchParams }) {
    const query = await searchParams;
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
    const posts = await res.json();

    if (query.sort === "desc") {
      posts.reverse();
    }

    return(
      <>
        <h2>Sort posts:</h2>
        <div>
          <Link href="/posts?sort=asc">Ascending</Link>
          <Link href="/posts?sort=desc">Descending</Link>
        </div>
        <h2>Posts</h2>
        {posts.map((post) => (
          <div key={post.id}>
            <h2>{post.title}</h1>
            <p>{post.body}</p>
          </div>
        ))}
      </>
    )
  }
  ```
    - Just like `params`, `searchParams` is a promise that needs to be awaited but we can again get values out on one line with `const sort = (await searchParams).sort;`
    - Like in React Router we can use the `Link` component to let users change the search params
</details>

<details><summary><h3>Next Postgres</h3></summary>

- Just like data fetching, we can make database calls through the `pg` package directly from our components in Next.js
  - All we need to do is import `pg`, create our `pg.Pool`, and query the database directly
    ```js
    {/* /posts/page.js */}
    import pg from "pg";

    export default async function PostsPage() {
      const db = new pg.Pool({
        connectionString: process.env.DB_CONN,
      });

      const posts = (await db.query("SELECT * FROM posts")).rows;

      return(
        <>
          {posts.map((post) => (
            <div key={post.id}>
              <h2>{post.title}</h1>
              <p>{post.body}</p>
            </div>
          ))}
        </>
      )
    }
    ```
    - Now the result of the query is an array that we can use on the rest of the page, often mapping through it to render the data
- We can do this in dynamic routes too and use the params in the query, usually with a `WHERE` clause to select only certain records
  ```js
  {/* /posts/[id]/page.js */}
  import pg from "pg";

  export default async function PostsPage({ params }) {
    const id = (await params).id;

    const db = new pg.Pool({
      connectionString: process.env.DB_CONN,
    });

    const post = (await db.query(`SELECT * FROM posts WHERE id = $1`, [id])).rows[0];

    return(
      <div>
        <h2>{post.title}</h1>
        <p>{post.body}</p>
      </div>
    );
  }
  ```
- Our `.env` file needs to go in the root of the project outside of the `/src` directory
  - We can access the environment variables in this file through the `process.env` object as usual, but Next creates this object for us without needing the `dotenv` package
- Instead of having to import `pg` and create a pool in every page that we want to write database queries in, we can create a file that exports the `db` variable so that we can import that directly
  ```js
  {/* /src/utils/db.js */}
  import pg from "pg";

  export const db = new pg.Pool({
    connectionString: process.env.DB_CONN,
  });
  ```
  - `utils` isn't a special directory but it's used for this type of utility and others that we want to export and use across the app
  - Now instead of the whole process we did before, we can just do `import db from "@/utils/db"` on each page that needs it
</details>

<details><summary><h3>Client and Server Components</h3></summary>

- SSR (Server-Side Rendering) is what differentiates Next.js from React but we can specify whether we want each component to be run on the client or server
- Server components are the norm and should make up most of our app, but we need to use client components for things that server components can't do
- Because all of the JS is run on the server before returning the finished page, server components can't run any JS after the page loads which means we can't use:
  - Events (onClick, onSubmit, etc.)
  - React hooks (useState, useEffect, etc.)
  - Browser APIs (document/window objects, localStorage, etc.)
- The benefits of server componets are what we've already covered:
  - Better performance
  - Direct API and database calls
- All components are server components by default and we need to add `"use client"` at the very top of the page or component to make it a client component
  ```js
  "use client"

  export default function CoolButton() {
    return(
      <button onClick={() => {console.log("I can only exist on the client!")}}>
        Click Me
      </button>
    )
  }
  ```
- Client components can be imported into server components with no problem, but server components can't be imported into client components
  - This means we want to keep our client components as small and self-contained as possible like a single button as in the example above
  - This also means we need to pay more attention to where we are using state as it must be entirely contained to client components
</details>

<details><summary><h3>Forms and Server Functions</h3></summary>

- Forms can be used on server components despite not being able to use event listeners, as we can use server functions
- Server functions are functions that run on the server upon form submission to submit our form data to a database without the need for event listeners or API routes
- There are a couple of steps for making server functions and connecting them to forms:
  - Create a named async function within the component function
  - Add `"use server"` on the first line of the function
    - Try not to think of this as the opposite of `"use client"`, we're not specifying a server component but a server function
  - Get the value of each input field in our form
  - Write a database query to use the data from the form
  - Create a form as normal but add an `action` prop to the `<form>` tag to run our server function
  ```js
  {/* /create-post/page.js */}
  import db from "@/utils/db";

  export default async function CreatePost() {
    async function handlePost(formData) {
      "use server";

      const title = formData.get("title");
      const body = formData.get("body");

      await db.query("INSERT INTO posts (title, body) VALUES ($1, $2)", [
        title,
        body,
      ]);
    }

    return(
      <>
        <form action={handlePost}>
          <label htmlFor="title">Enter a title</label>
          <input id="title" name="title"/>
          <label htmlFor="body">Post Content</label>
          <textarea id="title" name="title" placeholder="Write your post here..."/>
          <button type="submit">Submit</button>
        </form>
      </>
    )
  }
  ```
  - Server functions are a good place to use destructuring so that we don't have to make a long list of `formData.get` for larger forms
    ```js
    async function handlePost(formData) {
      "use server";

      const { title, body } = formData;

      await db.query("INSERT INTO posts (title, body) VALUES ($1, $2)", [
        title,
        body,
      ]);
    }
    ```
- This is much more straightforward than the way we've handled form submissions in React, so it's usually better to make forms in Next in server components
- Sometimes we don't want a full form for users to fill in but still want them to be able to manipulate the data in the database, such as with a 'delete' button, and there are a couple of options for this
  - We can make the button a client component and pass down the server function as a prop, the same way we'd pass down any function
    ```js
    {/* /post/[id]/page.js */}
    import db from "@/utils/db";
    import DeleteButton from "@/components/DeleteButton";
    
    export default async function IndividualPost({ params }) {
      const id = (await params).id;
      const post = (await db.query("SELECT * FROM posts WHERE id = $1", [id])).rows[0];

      async function handleDelete() {
        "use server";

        await db.query("DELETE FROM posts WHERE id = $1", [id])
      }

      return(
        <div>
          <h2>{post.title}</h2>
          <p>{post.body}</p>
          <DeleteButton handleDelete={handleDelete} />
        </div>
      );
    }
    ```
    ```js
    {/* /components/DeleteButton.jsx */}

    export default function DeleteButton({ handleDelete }) {
      return(
        <button onClick={handleDelete}>Delete Post</button>
      );
    }
    ```
  - We can handle it all on the server instead by making a form with no inputs and just a 'submit' button
    ```js
    {/* /post/[id]/page.js */}
    import db from "@/utils/db";
    import DeleteButton from "@/components/DeleteButton";
    
    export default async function IndividualPost({ params }) {
      const id = (await params).id;
      const post = (await db.query("SELECT * FROM posts WHERE id = $1", [id])).rows[0];

      async function handleDelete() {
        "use server";

        await db.query("DELETE FROM posts WHERE id = $1", [id])
      }

      return(
        <div>
          <h2>{post.title}</h2>
          <p>{post.body}</p>
          <form action={handleDelete}>
            <button type="submit">Delete Post</button>
          </form>
        </div>
      );
    }
    ```
    - It's not needed in this case but sometimes we'll need to include an input with `type="hidden"` to pass some other data through the `value` attribute
</details>

<details><summary><h3>Images in Next</h3></summary>

- Instead of the standard `<img/>` tag we use the built-in `<Image/>` component in Next.js
- This component improves on `<img/>` in a few ways:
  - It automatically serves the correct image size for your device without needing `srcset`
  - It prevents layout shift while images are loading
  - It uses lazy loading to improve performance by only loading the image when it's in view
  - It complains to us through the console if we don't include alt text (debatable if this is an improvement tbh)
- To use the Image component we first need to import it then we can use it like any other component
  ```js
  import Image from "next/image";

  export default function ImagePage() {
    return(
      <div>
        <Image
          src="/orangutan.png"
          alt="A lovely orangutan looking at the camera"
          height={500}
          width={500}
        />
        <Image
          src="/gorilla.png"
          alt="A big cool gorilla sitting on a rock"
          height={600}
          width={480}
        />
        <Image
          src="/chimp.png"
          alt="A happy chimpanzee swinging around"
          height={500}
          width={300}
        />
      </div>
    );
  }
  ```
  - While an `img` only strictly needs a `src` attribute, a Next `Image` needs `src`, `alt`, `height`, and `width` attributes
    - `src` and `alt` function the same way as we're used to
    - `height` and `width` set the *intrinsic* height and width in pixels that are used to determine the correct aspect ratio and avoid layout shift, but these are separate to the properties we set with CSS 
  - For files in the `/public` directory we can skip the whole import path and just use a single `/` for the public directory e.g. `/myImage.png` instead of `@/../public/myImage.png`
    - It's worth noting here too that we can use `@` in file paths in Next.js to refer to the `/src` directory from any file instead of needing a series of `..`
- As usual we can use an external image URL as the `src` instead of a file path but we need to configure the `next.config.mjs` file to allow the image host
  - By default, `next.config.mjs` exports an empty object called `nextConfig` and we want to add to this object
    ```js
    const nextConfig = {
      images: {
        remotePatterns: [
          {
            protocol: "https",
            hostname: "example.com",
            port: "",
            pathname: "/**",
            search: "",
          },
        ],
      },
    };
    ```
    - For the purposes of the bootcamp we may want to allow images from all hosts by setting hostname to `"**"`, but this is bad practice in actual production apps
      ```js
      const nextConfig = {
        images: {
          remotePatterns: [
            {
              protocol: "https",
              hostname: "**",
            },
          ],
        },
      };
      ```
- Another approach to the `Image` component is to statically import the image file which lets us skip the `height` and `width` attributes
  ```js
  import Image from "next/image";
  import orangutan from "/orangutan.png";
  import gorilla from "/gorilla.png";
  import chimp from "/chimp.png";

  export default function ImagePage() {
    return(
      <div>
        <Image
          src={orangutan}
          alt="A lovely orangutan looking at the camera"
        />
        <Image
          src={gorilla}
          alt="A big cool gorilla sitting on a rock"
        />
        <Image
          src={chimp}
          alt="A happy chimpanzee swinging around"
        />
      </div>
    );
  }
  ```
</details>

<details><summary><h3>Fonts in Next</h3></summary>

- Fonts are imported differently in Next.js as Google fonts comes built into the framework
- To use fonts from Google fonts we need to import them in `layout.js` from `"next/font/google"` then create an object with configuration option for the font
  ```js
  import { Geist, Geist_Mono } from "next/font/google";
 
  const geistSans = Geist({
    variable: "--font-geist-sans",
    subsets: ["latin"],
  });
 
  const geistMono = Geist_Mono({
    variable: "--font-geist-mono",
    subsets: ["latin"],
  });
  ```
- Geist and Geist_Mono are the default fonts used in Next but to find more we can `CTRL+click` or `CMD+click` on one of these fonts in the import to find the full list of fonts buried deep within `node_modules`
  - Some of these fonts have different options to configure in the object which we can see in the list of fonts, for example the font Fira_Mono has `weight`, `style`, `display`, `variable`, `preload`, `fallback`, `adjustFontFallback`, and `subsets` properties that we can configure in the objects
    ```js
    export declare function Fira_Mono<T extends CssVariable | undefined = undefined>(options: {
        weight: '400' | '500' | '700' | Array<'400' | '500' | '700'>;
        style?: 'normal' | Array<'normal'>;
        display?: Display;
        variable?: T;
        preload?: boolean;
        fallback?: string[];
        adjustFontFallback?: boolean;
        subsets?: Array<'cyrillic' | 'cyrillic-ext' | 'greek' | 'greek-ext' | 'latin' | 'latin-ext' | 'symbols2'>;
    }): T extends undefined ? NextFont : NextFontWithVariable;
    ```
    - We can see that the `weight` property is required as it doesn't have a `?` to mark it as an optional property so usually we don't include most of the properties
    - Each of the properties also states the values or data types that it can take
  - Here's what importing Fira_Mono with these options could look like
    ```js
    import { Fira_Mono } from "next/font/google";

    const firaMono = Fira_Mono({
      weight: '500',
      variable: '--font-fira-mono',
      subsets: ['latin'],
    });
    ```
- With our font imported and configured we can now set it as the font by adding it as a `className` in the JSX returned by our `RootLayout` function in the `<html>` or `<body>` tag
  ```js
  export default function RootLayout({ children }) {
    return (
      <html lang="en">
        <body
          className={`${firaMono.className} antialiased`}
        >
          {children}
        </body>
      </html>
    );
  }
  ```
- If we don't want to use the font all through our app by adding it to the `<html>` or `<body>` tags, we can export the font and import it into the components we want to use it in
  ```js
  {/* layout.js */}
  import { Fira_Mono } from "next/font/google";

  export const firaMono = Fira_Mono({
    weight: '500',
    variable: '--font-fira-mono',
    subsets: ['latin'],
  });
  ```
  ```js
  {/* /app/page.js */}
  import firaMono from "./layout.js";

  export default function HomePage() {
    return(
      <div>
        <h1 className={`${firaMono.className}`>
          Look at this cool font!
        </h1>
        <p>Here's some content, this bit has the default font</p>
      </div>
    )
  }
  ```
- We can use local fonts too, not just those from Google fonts, with a similar process but importing `localFont` from `"next/font/local"` and setting a `src` property in the config object
  ```js
  {/* layout.js */}
  import localFont from "next/font/local";

  const magicCards = localFont({
    src: "/fonts/MagicCardsNormal.ttf",
  });

  export default function RootLayout({ children }) {
    return (
      <html lang="en">
        <body
          className={`${magicCards.className} antialiased`}
        >
          {children}
        </body>
      </html>
    );
  }
  ```
</details>

<details><summary><h3>Styling in Next</h3></summary>

- Next.js has a few options for styling similarly to React
- The `globals.css` file in `/app` works the same as a standard `style.css` file where we can style with our standard CSS selectors
- We can use Tailwind too as long as we say yes to it when setting up the app with `create-next-app`
  - We can also extend Tailwind with new variables in the `globals.css` file using the `@theme` directive
  ```css
  @theme {
    --color-w-blue: #408FC0;
  }
  ```
  ```js
  {/* layout.js */}
  export default function RootLayout({ children }) {
    return (
      <html lang="en">
        <body
          className={`${geistSans.variable} ${geistMono.variable} bg-w-blue antialiased`}
        >
          {children}
        </body>
      </html>
    );
  }
  ```
  - We can use themes for values such as colours and fonts to make them usable through Tailwind utility classes
- Unique to Tailwind are CSS modules where we can write standard CSS and import it into individual components or pages
  - We create a `.module.css` file and write our CSS in there
  ```css
  {/* /components/footer.module.css */}

  .footer {
    background: papayawhip;
    display: flex;
    justify-content: space-around;
    align-items: center;
  }

  .list {
    display: flex;
    flex-direction: column;
    padding: 1rem 2rem;
  }
  ```
  - Next we import this into the component we want to use it in and use the classes
    ```js
    {/* /components/Footer.jsx */}
    import styles from "./footer.module.css";

    export default function Footer() {
      return(
        <footer className={styles.footer}>
          <ul className={styles.list}>
            <li>Item 1</li>
            <li>Item 2</li>
            <li>Item 3</li>
          </ul>
          <ul className={styles.list}>
            <li>Item 4</li>
            <li>Item 5</li>
            <li>Item 6</li>
          </ul>
        </footer>
      )
    }
    ```
    - The only thing to remember is that we need to give a name to the imported CSS module and preface any class names with this name e.g. `className={headerStyles.myClass}`
</details>

<details><summary><h3>Deploying to Vercel</h3></summary>

- Next.js is created by Vercel which is handy because it means we can deploy Next apps easily to vercel.com
- We need to connect Vercel to our GitHub account so we can select our repos to deploy our apps from
- From the Vercel dashboard go to the 'Add New' dropdown and select 'Project'
  - Select the repo we want to deploy from
  - We shouldn't need to change the build command, install command, or output directory
  - In the environment variables dropdown we can add the environment variables from our `.env` file(s)
  - Click deploy and we're done, it's a lot simpler than Render!
- We can't choose the region for the server that runs the code to build our app, but we can choose the region that our server functions run in
  - After deployment navigate to the project from the dashboard and go to the Settings tab (along the top) and the Functions tab (down the side)
  - Now we can choose the function region, though with the free version of Vercel we can only have one at a time which for us should be 'London, United Kingdom (West) - eu-west-2'
</details>
