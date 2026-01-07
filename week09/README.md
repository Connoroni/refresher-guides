# Week 9 Refresher Guide

### User Authentication with Clerk
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

### Clerk With SQL
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

### Clerk Route Protection
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

### Component Libraries
- 

### Error and Not Found Pages
- 

### Loading States and Suspense
- 

### Animations with Motion
- 

### TypeScript
- 
