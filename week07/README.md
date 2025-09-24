# Week 7 Refresher Guide

### React Router
- React Router is a package that lets us use multiple routes in our React apps and create Single Page Applications (SPAs)
- An SPA is an app that is built with just one `index.html` files even if we use different routes that look like entirely different pages
  - This is different to multi-page apps we have seen before that have multiple `index.html` files with each one being a page
  - React supports this because our whole app is rendered in a `root` div within our `index.html` file
- Routes in React Router let us render different components depending on the route that we're currently on
  - We need to install React Router like any other library with `npm i react-router`
- We import three main components from React Router:
  - BrowserRouter - This is a provider that we wrap our app in to let it use the features of React Router
  - Routes - This component contains the content to be swapped out depending on the route we're on, we nest all of our `Route` components inside this
  - Route - Each `Route` component takes a `path` and `element` prop to render a certain element when we're on a certain route
- Here's what a (very) basic React Router app could look like
  ```js
  import { BrowserRouter, Routes, Route } from "react-router";
  
  export default function App() {
    return(
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<h1>Home</h1>} />
          <Route path="/about" element={<h1>About Us</h1>} />
          <Route path="/contact-us" element={<h1>Contact Us</h1>} />
        </Routes>
      </BrowserRouter>
    )
  }
  ```
  - This example has three routes with each one rendering an `<h1>` element with the title of the page on the `/about`, `/contact-us`, and the `root` routes
    - The root route is represented by just `/` like when we make server endpoints
  - Usually we won't be rendering a single element but an entire component
- Instead of putting `BrowserRouter` in our `App` component we can put it in `main.jsx` and wrap the `App` component itself in the provider, which ensures that we don't leave anything outside of the provider by accident
  ```js
  {/* main.jsx */}
  import { StrictMode } from "react";
  import { createRoot } from "react-dom/client";
  import { BrowserRouter } from "react-router";
  import "./index.css";
  import App from "./App.jsx";
  
  createRoot(document.getElementById("root")).render(
    <StrictMode>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </StrictMode>
  );
  ```
  - We still use our `Routes` and `Route` in `App.jsx` but don't need to use or import `BrowserRouter`
- We can create a component to represent a page then use this as the `element` prop of the `Route` component
  ```js
  {/* App.jsx */}
  import { Routes, Route } from "react-router";
  import About from "./components/About.jsx";
  
  export default function App() {
    return(
      <Routes>
        <Route path="/" element={<h1>Home</h1>} />
        <Route path="/about" element={<About/>} />
      </Routes>
    )
  }
  ```
  ```js
  {/* About.jsx */}
  
  export default function About() {
    return(
      <>
        <h1>About Us</h1>
        <img src="/ourLogo.png" />
        <p>Here is some information about this company. It doesn't actually exist.</p>
      </>
    )
  }
  ```
  - This will render our `About` component when we're on the `/about` route
- We can add a special `Route` for a 'not found' page to render a component when we're on any route that isn't one of the ones we've specified, we do this by using `*` as the `path` prop
  ```js
  {/* App.jsx */}
  import { Routes, Route } from "react-router";
  import About from "./components/About.jsx";
  import NotFound from "./components/NotFound.jsx";
  
  export default function App() {
    return(
      <Routes>
        <Route path="/" element={<h1>Home</h1>} />
        <Route path="/about" element={<About/>} />
        <Route path="*" element={<NotFound/>} />
      </Routes>
    )
  }
  ```
  ```js
  {/* NotFound.jsx */}
  import { Link } from "react-router";

  export default function NotFound() {
    return (
      <>
        <h1>Uh Oh!</h1>
        <p>Whoops, we couldn't find that page!</p>
        <p>Click <Link to="/">here</Link> to return to the home page</p>
      </>
    )
  }
  ```
  - Here we're also using the `Link` component provided by React Router, which is very similar to an anchor tag but takes a prop of `to` instead of an attribute of `href`

### Dynamic and Nested Routes
- Sometimes we don't want to hard code every single page for every route and instead want data on the page depending on a parameter in the URL, this is a dynamic route
- Examples of dynamic routes are posts and accounts on social media sites: no developer has gone through and made a page for every post and every account but instead they create a dynamic route that can render any account's data
  - This could look something like `twitter.com/users/myusername` which would render the data for a user called 'myusername'
    - Often we'd be querying a database for an account with a username of 'myusername' to get that data
- The syntax for a dynamic route is a colon `:` followed by a parameter which will be replaced by whatever route we navigate to
  ```js
    import { Routes, Route } from "react-router";
    import UserPage from "./components/UserPage";
    
    export default function App() {
      return(
        <Routes>
          <Route path="/users/:username" element={<UserPage/>} />
        </Routes>
      )
    }
  ```
  - Now if we navigate to a route like `website.com/users/steve` or `website.com/users/susan` then the `UserPage` component will be rendered
- We want to make a `UserPage` component that will render different data depending on the route we're on so we use the `useParams` hook that comes from React Router
  - Similarly to `useState` we destructure the object returned by `useParams` so we can use it in our component
  ```js
  import { useParams } from "react-router";
  
  export default function UserPage() {
    const { username } = useParams();
    return(
      <>
        <h2>{username}</h2>
        <p>This is the profile belonging to {username}</p>
      </>
    )
  }
  ```
- Now if we go to a route like `website.com/users/steve` or `website.com/users/susan` then the value of `username` will be steve or susan so those will be rendered
- A nested route is one that is nested inside another route (just like an HTML element can be nested inside another) which we do by nesting one `Route` component inside another
  ```js
    import { Routes, Route } from "react-router";
    import UserPage from "./components/UserPage";
    
    export default function App() {
      return(
        <Routes>
          <Route path="/users/:username" element={<UserPage/>}>
            <Route path="posts" element={<UserPosts/>}/>
            <Route path="likes" element={<UserLikes/>}/>
          </Route>
        </Routes>
      )
    }
  ```
  - Now we'll have two different routes nested in the dynamic `/users/:username` route that each render a different component depending on which route we're on: `/users/:username/posts` or `/users/:username/likes`
- To get the correct component to render within another component on a nested route we need to use the `Outlet` component from React Router
  - This goes in the parent route's component, in this case `UserPage` and will render the component for whichever child route we're on in place of the `Outlet` component
  ```js
  {/* UserPage.jsx */}
  import { Outlet, Link, useParams } from "react-router";
  
  export default function UserPage() {
    const { username } = useParams();
    return(
      <>
        <h2>{username}</h2>
        <p>This is the profile belonging to {username}</p>
        <div>
          <Link to={`/users/${username}/posts`}>Posts</Link>
          <Link to={`/users/${username}/likes`}>Likes</Link>
        </div>
        <Outlet />
      </>
    )
  }
  ```

### React Router Query Strings
- Query strings let us add extra data to the URL beyond the parameters for dynamic pages, usually for filtering or sorting data on the page
  - Query strings come after a `?` in a URL and are written as key-value pairs similar to JavaScript objects e.g. `sort=desc`
    - If there are multiple key-value pairs we separate them with an `&` e.g. `userid=5&sort=asc&stealdata=1`
  - A typical example would be something like `website.com/posts?sort=asc` but some websites use them differently such as YouTube where the video id is stored in a query string e.g. `https://www.youtube.com/watch?v=dQw4w9WgXcQ`
- We can get the value of query strings by using the `useSearchParams` hook from React Router returns an array containing an object of the current query string(s), and a function to let us change the query string(s) similar to `useState` which returns the state variable and the mutation function
  ```js
  {/* Posts.jsx */}
  import { useSearchParams } from "react-router";
  
  export default function PostsPage() {
    const [searchParams] = useSearchParams();
    return(
      <>
        <h1>Posts</h1>
        <div>
          <p>Sort by: {searchParams.sort}</p>
        </div>
        {posts
        .sort((a,b) => searchParams.sort === 'asc' ? a.title > b.title : b.title > a.title)
        .map((post) => (
          <div>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </div>
        ))}
      </>
    )
  }
  ```
  - Now if we navigate to the route containing the component above, the posts will be sorted in ascending or descending order depending on the value of `sort` in the query string: `example.com/posts?sort=asc` or `example.come/posts?sort=desc`
    - In this example it doesn't matter if we're sorting by `desc` or anything else as long as we're only checking if `searchParams.sort` is `asc` or not `asc`
- To be able to change values in our query strings we use the mutation function given by `useSearchParams`
  ```js
  {/* Posts.jsx */}
  import { useSearchParams } from "react-router";

 
  export default function PostsPage() {
    const [searchParams, setSearchParams] = useSearchParams();
    function handleChange(event) {
      setSearchParams({ sort: event.target.value });
    };
    return(
      <>
        <h1>Posts</h1>
        <form>
          <label>Sort by: {searchParams.sort}</label>
          <select value={searchParams.get("sort") || ""} onChange={handleChange}>
            <option value="asc">Ascending</option>
            <option value="desc">Descending</option>
          </select>
        </form>
        {posts
        .sort((a,b) => searchParams.sort === 'asc' ? a.title > b.title : b.title > a.title)
        .map((post) => (
          <div>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </div>
        ))}
      </>
    )
  }
  ```
  - This form would also work as a separate component and still be able to access `searchParams`
  - If instead of a form we want links to different 

### React Forms and Validation
- 

### React Timers and Polling
- 

### Design Methodologies
- 

### Relational Databases
- 

