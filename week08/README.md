# Week 8 Refresher Guide

### Next.js Intro
- Next.js is a popular framework that builds on React
  - React is great for building nice UIs but it doesn't include some of the features we need to build full stack apps without adding third-party libraries
- The key difference is that Next.js uses SSR (Server Side Rendering) which means all of the JavaScript used to generate the HTML is run on the server and the completed HTML document is returned all at once, keeping the amount of JavaScript that gets sent to the client so the page is downloaded faster
  - This is in contrast to React (and vanilla JS) where the code is run on the client and the page is created as it runs
- Another key feature of Next.js is that it includes routing similar to what React Router allows us to do

### Setting Up a Next Project
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

### File Routing
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

### Metadata
- 

### Data Fetching in Next
- 

### Next Postgres
- 

### Client and Server Components
- 

### Forms and Server Functions
- 

### Images in Next
- 

### Fonts in Next
- 

### Styling in Next
- 

### Deploying to Vercel
- 
