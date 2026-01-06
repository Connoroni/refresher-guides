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

### Clerk With SQL
- 

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
