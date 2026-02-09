# Week 9 Refresher Guide

<details><summary><h3>User Authentication with Clerk</h3></summary>

- Authentication means checking the credentials (e.g. username and password) of a user so they can create and login to an account
- There are a lot of authentication libraries out there but the one we're going to use is Clerk which has a lot of handy features and can be integrated nicely with Next.js
- To setup Clerk we first set up a Next.js app as normal then follow the instructions in Clerk's [Next.js quickstart guide](https://clerk.com/docs/nextjs/getting-started/quickstart)
  - First we run `npm i @clerk/nextjs` to install the Clerk library
  - Next we create a `proxy.js` file in the `/src` directory
  - In `proxy.js` we import `clerkMiddleware` from Clerk then export the `clerkMiddleware()` function and a `config` object
    ```js
    // proxy.js
    import { clerkMiddleware } from '@clerk/nextjs/server'
    
    export default clerkMiddleware()
    
    export const config = {
      matcher: [
        // Skip Next.js internals and all static files, unless found in search params
        '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
        // Always run for API routes
        '/(api|trpc)(.*)',
      ],
    }
    ```
      - Don't worry, we don't need to remember the config object, we can find it in quickstart guide
  - Next we import `<ClerkProvider>` and any other Clerk components into our app
    ```js
    // layout.js
    import {
      ClerkProvider,
      SignInButton,
      SignUpButton,
      SignedIn,
      SignedOut,
      UserButton,
    } from '@clerk/nextjs'

    {/* ... */}

    export default function RootLayout() {
      return (
        <ClerkProvider>
          <html>
            <body>
              <header className="flex justify-end items-center p-4 gap-4 h-16">
                <SignedOut>
                  <SignInButton />
                  <SignUpButton />
                </SignedOut>
                <SignedIn>
                  <UserButton />
                </SignedIn>
              </header>
              {children}
            </body>
          </html>
        </ClerkProvider>
      )
    }
    ```
    - This is the default Clerk header that's covered in the quickstart guide, we don't need this exact setup but we should include these components somewhere in the app to get the functionality we want
    <details><summary>Explanation of each of these Clerk components:</summary>
      - `&lt;ClerkProvider&gt;` has to wrap the entire app similarly to when we used `&lt;BrowserRouter&gt;` in React Router so that Clerk features are available to us
      - `&lt;SignInButton&gt;` and `&lt;SignUpButton&gt;` take the user to the sign in or sign up page respectively
      - `&lt;SignedIn&gt;` and `&lt;SignedOut&gt;` only render their children when the user is signed in or signed out respectively
      - `&lt;UserButton&gt;` shows the user the account they have created with Clerk
      - `&lt;SignOutButton&gt;` isn't included in the default header but it lets users sign out of their account
    </details>
  - Now we can run our Next app as normal and sign up using the sign up button to create an account through Clerk
  - Finally we want to claim our Clerk application by clicking the 'Claim your application' button that should appear in the bottom corner of the app
    - You will need to create a Clerk account to link your app to first
- One of the most useful parts of Clerk is the async `auth()` function which returns the `auth` object for the current user
  - We can import auth in a server component and pull values like `userId` out of it by destructuring
    ```js
    import { auth } from "@clerk/nextjs/server";

    export default async function ProfilePage() {
      const { userId } = await auth();

      console.log("Your Clerk user ID is:", userId);

      return(
        <>
          <h1>Hello user {userId}</h1>
        </>
      )
    }
    ```
  - Keep in mind that this can't be used in client components
  - This is useful when we want to have data (such as posts) associated with users as we want to use the userId of the user who created the data to associate the data with the correct account
    - To do this though we'll need to connect our Clerk accounts to a database storing user profiles
</details>

<details><summary><h3>Clerk With SQL</h3></summary>

- Clerk on its own is great but often we'll want to store more data on users especially if we want to have data associated with each user
- Clerk creates accounts but the only data that these generally have on users is their email and user id so we'll have to collect some more
- We should create tables for users and posts (or any other kind of data that users are creating) with a one-to-many relationship
  ```
  CREATE TABLE IF NOT EXISTS users (
    id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    clerk_id TEXT UNIQUE NOT NULL,
    username VARCHAR(255) NOT NULL,
    bio TEXT
  );
  
  CREATE TABLE IF NOT EXISTS posts (
    id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    user_id INT REFERENCES users(id),
    title VARCHAR(255),
    content TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    img TEXT,
    likes INT DEFAULT 0
  );
  ```
- We can use a form like normal to get users to create a profile after signing up, but use the `auth` function to get their `userId` and submit this along with the form data
  ```js
  {/* /create-profile/page.js */}
  import { db } from "@/utils/dbConn";
  import { auth } from "@clerk/nextjs/server";
  import { redirect } from "next/navigation";
  import { revalidatePath } from "next/cache";
  
  export default async function CreateProfile() {
    const { userId } = await auth();
  
    async function handleSubmit(formData) {
      "use server";
      const { username, bio } = Object.fromEntries(formData);
  
      await db.query(
        "INSERT INTO users (clerk_id, username, bio) VALUES ($1, $2, $3)",
        [userId, username, bio]
      );
  
      revalidatePath("/user");
      redirect("/user");
    }
  
    return (
      <form action={handleSubmit}>
        <label htmlFor="username">Enter a username:</label>
        <input type="text" name="username" />
        <label htmlFor="bio">Enter a bio:</label>
        <textarea name="bio" />
        <button type="submit">Submit</button>
      </form>
    );
  }
  ```
  - Now we have an account that has the clerk ID associated with the user's account details so we can do all sorts of things:
    - Add ID with created posts to associate them with the correct user
    - Show the username instead of userId for users who created posts
    - Allow users to only edit or delete their own posts
- The most common next step after this will be adding the ID to posts to be used as the foreign key
  ```js
  {/* /create-post/page.js */}
  import { db } from "@/utils/dbConn";
  import { auth } from "@clerk/nextjs/server";
  
  export default async function CreatePost() {
    const { userId } = await auth();
    const sqlId = (
      await db.query("SELECT id FROM users WHERE clerk_id = $1", [userId])
    ).rows[0].id;
  
    if (!sqlId) {
      return (
        <p>
          You&apos;re not signed in or you don&apos;t have a profile! Please go
          make one!
        </p>
      );
    }
  
    async function handlePost(formData) {
      "use server";
      const { title, content, image } = Object.fromEntries(formData);
      await db.query(
        "INSERT INTO posts (title, content, img, user_id) VALUES ($1, $2, $3, $4)",
        [title, content, image, sqlId]
      );
    }
  
    return (
      <form action={handlePost}>
        <label htmlFor="title">Enter a title:</label>
        <input type="text" name="title" />
        <label htmlFor="content">Enter the content:</label>
        <input type="text" name="content" />
        <label htmlFor="image">Enter an image URL:</label>
        <input type="url" name="image" />
        <button type="submit">Submit</button>
      </form>
    );
  }
  ```
  - We could cut out a few lines by using `clerk_id` as the foreign key, but the problem with this is that it makes our app rely entirely on Clerk which isn't great for production apps that might switch to a different authentication library later on
  - We're using a conditional here with `if (!sqlId)` to check if the user has a profile, which is separate to `auth` that lets us check if they have an account
    - To get more fancy, we could add a redirect here to send users to the create-profile page
</details>

<details><summary><h3>Clerk Route Protection</h3></summary>

- Often on our apps we want certain routes to be accessible only to users who are signed in, so we need to **protect** those routes
- When we create our `proxy.js` file we export the `clerkMiddleware()` function from it, but we can add an argument to this function to protect our routes
  - We use a second function, `createRouteMatcher()`, to define which strings we want to be protected
    ```js
    // proxy.js
    import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server'

    const isProtectedRoute = createRouteMatcher(['/dashboard(.*)', '/profile(.*)'])
    
    export default clerkMiddleware(async (auth, req) => {
      if (isProtectedRoute(req)) await auth.protect()
    })
    
    export const config = {
      matcher: [
        // Skip Next.js internals and all static files, unless found in search params
        '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
        // Always run for API routes
        '/(api|trpc)(.*)',
      ],
    }
    ```
      - `createRouteMatcher` takes an array containing the routes we're protecting as strings, with the `(.*)` meaning that it includes anything after the route e.g. both `/profile` and `/profile/cool-guy` will be protected
- We can also do the opposite and protect all routes except certain routes that we want to be public just by flipping the conditional with `!`
  - We use `createRouteMatcher` again but this time we include only the routes that we want to keep public
    ```js
    // proxy.js
    import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server'

    const isPublicRoute = createRouteMatcher(['/about(.*)', '/sign-in(.*)', '/sign-up(.*)'])
    
    export default clerkMiddleware(async (auth, req) => {
      if (!isPublicRoute(req)) await auth.protect()
    })
    
    export const config = {
      matcher: [
        // Skip Next.js internals and all static files, unless found in search params
        '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
        // Always run for API routes
        '/(api|trpc)(.*)',
      ],
    }
    ```
</details>

<details><summary><h3>Component Libraries</h3></summary>

- Component libraries are useful resources that contain pre-made components for us to import, customise, and use in our apps instead of having to make everything from scratch
- There are a lot of options out there but the one we'll be using as an example is [Radix](https://www.radix-ui.com/)
- We need to install Radix from npm like any other package or library with `npm i radix-ui`
  - Alternatively we can install just the component that we want e.g. `npm i @radix-ui/react-dialog`
- We then import only the component(s) that we want from Radix in the component that we want to use them in e.g. `import { dialog } from "radix-ui";`
  - It's a good idea to have a create a `.jsx` file for each component we're importing e.g. if we import a Dialog component we do it in a `Dialog.jsx` file and use it like any other component
- The best guide to follow is the one in the Radix docs for the component you're using e.g. [this page](https://www.radix-ui.com/primitives/docs/components/dialog) for the Dialog component
  - Don't be intimidated by the example at the top of the page, this is showing what we can do but it's not the template we should use
  - Radix has an 'anatomy' section for each of its components which contains the different parts of the component that we can customise so we should copy this setup into our own component
  <details><summary>Anatomy for the Dialog component</summary>
    ```js
    import { Dialog } from "radix-ui";

    export default () => (
    	<Dialog.Root>
    		<Dialog.Trigger />
    		<Dialog.Portal>
    			<Dialog.Overlay />
    			<Dialog.Content>
    				<Dialog.Title />
    				<Dialog.Description />
    				<Dialog.Close />
    			</Dialog.Content>
    		</Dialog.Portal>
    	</Dialog.Root>
    )
    ```
    - Because of the way we're used to writing components we probably want to change the component function to be a named function e.g. `export default function Dialog() {}`
  </details>
  - There is an explanation for each of the parts that make up the component and the different props and content we can add to each one
    - Some of these interact with state so we need to use `useState`, e.g. for things like the boolean `open` state for Dialog
</details>

<details><summary><h3>Error and Not Found Pages</h3></summary>

- By default we get some scary looking pages and messages when an error occurs or when we navigate to a page that doesn't exist, but this isn't very user friendly
- Next has file conventions (like `layout.js` and `page.js`) that we can use to create files to be shown when users encounter an error including a special one for a 404
- We can create a `not-found.js` page to handle 404 errors, meaning the page the user has navigated to can't be found
  ```js
  {/* /users/[id]/not-found.js */}
  import Link from "next/link";

  export default function UserNotFound() {
    return(
      <div>
        <h2>Sorry, we couldn&apos;t find that user</h2>
        <Link href="/">Take me home</Link>
      </div>
    );
  }
  ```
  - These go in a dynamic route in the same location as the `page.js`
  - We need to run the `notFound()` function provided by Next, so think about the conditions that should trigger this (usually if the relevant data can't be found in the database)
    ```js
    {/* /users/[id]/page.js */}
    import { db } from "@/utils/db.js";
    import { notFound } "next/navigation";

    export default async function UserPage() {
      const { id } = await params;

      const user = (await db.query(`SELECT * FROM users WHERE id = $1`, [id])).rows;

      if (!user.length) {
        notFound();
      };

      return (
        {/* Your regularly scheduled page content here */}
      )
    }
    ```
      - The `notFound()` function sends users to the nearest `not-found` page that we've created
- We also have error pages for any errors that aren't a `404` with an `error.js` or `global-error.js`
  - We can have an `error.js` in our `app` directory or in any individual route

    - Users will be shown the nearest `error.js` when they encounter an error
  - `global-error.js` is the default used across the whole app, but only works in a production environment so we can't test it in localhost
    - The file looks the same as any `error.js` just with a different file name
</details>

<details><summary><h3>Loading States and Suspense</h3></summary>

- Next gives us a few options to add loading states to our app for while we're waiting for components to load
  - These can be helpful when querying the database and awaiting data, or when we have large images that take a long time to load
- We can create a `loading.js` file in the same location as the relevant `page.js` and use it to set a component that appears while waiting for the finished page to be loaded
  ```js
  {/* loading.js */}

  export default function Loading() {
    return (
      <div>
        <p>Some content is loading...</p>
      </div>
    );
  }
  ```
- We can also use the `<Suspense>` component and a `fallback` prop to appear in place of specific components that we're waiting for, instead of having to wait for the whole page to load
  - That's the key difference between `loading.js` and `<Suspense>`: one of them appears instead of the whole page while the other appears in place of just the component(s) nested inside it
  ```js
  {/* /posts/[id]/page.js */}
  import { db } from "@/utils/db.js";
  import { Suspense } from "react";

  export default async function PostsPage({ params }) {
    const { id } = await params;

    const post = (await db.query(`SELECT * FROM posts WHERE id = $1`, [id])).rows[0];

    return(
      <Suspense fallback={<p>Loading post...</p>}>
       <div>
         <h2>{post.title}</h1>
         <p>{post.body}</p>
       </div>
      </Suspense>
    );
  }
  ```
    - Here we're using just a `p` tag but we can substitute this for a whole component instead
- We don't have to make our own fancy loading components, we can use animated components from libraries like `react-spinners` in either `loading.js` or a Suspense fallback
  - Be aware that we have to create these animated components as client components
</details>

<details><summary><h3>Animations with Motion</h3></summary>

- Motion (fka Framer Motion) is an animation library that allows us to add animations to React components
- We install Motion with `npm install motion@12.0.0` (or `npm install motion` for the latest version)
- Animated Motion components need to be client components as the animations are handled by the browser
  - Even though the animated component itself is a client component we can still import it into server components and use it within those
- The basic animated component is `<motion>` but we can use define HTML elements within that e.g. `<motion.div>` or `<motion.button>`
  - We can give this component props to define how we want it to animate, with the most basic ones being `initial` (the initial styling) and `animate` (how we want the styling to change)
    - These props can take any CSS value such as `opacity` or `background-color`
  ```js
  "use client";
  import { motion } from "motion/react";

  export default function AnimatedComponent() {
    return (
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: 100 }}
      >
        <p>I&apos;m in an animated div!</p>
      </motion.div>
    );
  }
  ```
- There are a whole host of other props we can use too such as:
  - `animate` - The target for when the component enters the DOM, other props give animation targets for other situations
  - `exit` - Animation when the component leaves the DOM (needs to be wrapped with `<AnimatePresence>`)
  - `whileInView` - Animation when the component enters the viewport
  - `whileHover` - Animation when we hover over the component
  - `whileTap` - Animation when we click or tap the component
  - `transition` - Defining how the component transitions from the initial styling to the animation target
- There are other components, hooks, props, and concepts we can read about them in the docs [here](https://motion.dev/docs/react-animation)
  - The Motion docs are alright but it's not as easy to find what we're looking for as it is in the Next.js or React docs
    - Some of the examples and tutorials are behind a paywall too which isn't great
</details>

<details><summary><h3>TypeScript</h3></summary>

- 
</details>
