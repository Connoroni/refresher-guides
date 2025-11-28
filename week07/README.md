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
        {searchParams.sort === "asc" ? posts.sort() : posts.sort().reverse()}
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
  - If instead of a form we want links to different query strings then we can do this using the `Link` component and set the `to` prop to the URL including the query string
    ```js
    <Link to="/?sort=asc>Ascending</Link>
    <Link to="/?sort=desc">Descending</Link>
    ```

### React Forms and Validation
- There are a few ways to write forms in React but the one we'll cover is a controlled form that stores the value in state whenever the values change, and submits those stored values when we submit the form
- We start by making a form like normal in JSX using `form`, `label`, `input`, and `button` elements but with only a `name` prop not an `id`
  ```js
  {/* Form.jsx */}
  export default function Form() {
    return (
      <>
        <form>
          <label htmlFor="username">Username</label>
          <input type="text" name="username" />
          <button type="submit">Submit</button>
        </form>
      </>
    )
  }
  ```
- Next we store the create a state variable to store the value of our username input, and declare a function to be an event handler for an `onchange` event that will run every time we update the value in our input
  ```js
  {/* Form.jsx */}
  export default function Form() {
    const [username, setUsername] = useState('');

    function handleChange(event) {
     setUsername(event.target.value)
    }

    return (
      <>
        <form>
          <label htmlFor="username">Username</label>
          <input type="text" name="username" onchange={handleChange} />
          <button type="submit">Submit</button>
        </form>
      </>
    )
  }
  ```
    - We're using the event object here that gets passed as an argument when the function is called, with the target property referring to the specific input and the value property being the value in that input element
- We also want to add a function to handle form submission as usual, here we'll just log the values but we could use fetch here to send a POST request
  ```js
  {/* Form.jsx */}
  export default function Form() {
    const [username, setUsername] = useState('');

    function handleChange(event) {
     setUsername(event.target.value)
    }

    function handleSubmit(event) {
      event.preventDefault()
      console.log("Username: ", username)
    }

    return (
      <>
        <form>
          <label htmlFor="username">Username</label>
          <input type="text" name="username" onchange={handleChange} />
          <button type="submit">Submit</button>
        </form>
      </>
    )
  }
  ```
- This is all well and good for one input, but most forms are going to have more than one input and we don't want to make a separate state and a separate onchange event handler for each one
- If we want multiple inputs we need to make an object to store all of our form values which we'll store in state, then we can update the relevant property when the matching input field is changed
  ```js
  {/* Form.jsx */}
  export default function Form() {
    const [formValues, setFormValues] = useState({
        username: "",
        email: "",
        age: "",
      });

    function handleChange(event) {
     setFormValues({
        ...formValues,
        [event.target.name]: event.target.value,
       })
    }

    function handleSubmit(event) {
      event.preventDefault()
      console.log("Form Values: ", formValues)
    }

    return (
      <>
        <form>
          <label htmlFor="username">Username</label>
          <input type="text" name="username" />
          <button type="submit">Submit</button>
        </form>
      </>
    )
  }
  ```
    - Here we're using the spread operator `...` which takes the existing properties in the object, and we're using dyanmic keys (`[event.target.name]`) to target different properties in the object depending on which input field we edit
      - Dynamic keys can be used elsewhere to let us access a value as a property name instead of hardcoding it
      - What this achieves in combination is to give us access to the formValues object and change the value in an individual property when we change the value of any of our inputs

### React Timers and Polling
- Timers in React need to be handled in `useEffect` as they interact with features that aren't controlled by React, just like when we use fetch
- Remember that useEffect lets us run some code once when the component mounts (the first render) and not need to be re-run on each render
  - We want timers to be added (and other side-effects to be run) on mounting but not be added again each time the component re-renders or we'd end up with duplicate timers running
- Other than being wrapped in `useEffect` our timers look pretty similar to vanilla
  ```js
  {/* DirtyTimer.jsx */}

  export default function DirtyTimer() {
    console.log("Dirty timer rendered");
    const [counter, setCounter] = useState(0);

    useEffect(() => {
      console.log("Effect running...");
      setInterval(() => {
        setCount((currentCount) => currentCount + 1);
      }, 1000);
    }, []);

    return (
      <div>
        <p>{counter}</p>
      </div>
    );
  }
  ```
  - This looks good, but our timer will actually run twice because of a feature of React called `StrictMode`
- In React we use `StrictMode` in development to test for bugs, and one thing that this does is to re-render our components and re-run our effects an extra time to catch any bugs
  - Because our effect runs twice, that means that our timer will be added twice which we don't want
  - We need to add a cleanup function to remove the timer at the end of our useEffect, which we do by adding a `return` statement to the end of our effect's callback function
- For our cleanup function here we want to use the `clearInterval` method, but we need to give our interval a name so we can reference it to clear it
  ```
  js
  {/* CleanTimer.jsx */}

  export default function CleanTimer() {
    console.log("Clean timer rendered");
    const [counter,setCounter] = useState(0);

    useEffect(() => {
      console.log("Effect running...");
      const myTimer = setInterval(() => {
        setCount((currentCount) => currentCount + 1);
      }, 1000);

      return () => {
        console.log("Cleaning up the timer");
        clearInterval(myTimer);
      }
    }, []);

    return (
      <div>
        <p>{counter}</p>
      </div>
    );
  }
  ```
  - With the cleanup function we will only ever have one timer running at once
- This same syntax can be used whenever we want to ensure that something is only added/created once, maybe to do direct DOM manipulation like adding event listeners

### Design Methodologies
- Design methodologies build on the design principles that we've covered previously, this time thinking about how we can actually implement these principles
- Different design methodologies go in and out of fashion, here is a list of some of the better known ones:
  - Skeumorphism
    - Representing elements as real world objects
    - Think about older app icons like Instagram being a vintage camera or YouTube on iOS being an old TV screen
      - Older versions of iOS and macOS are heavy on the skeumorphism
    - It can be useful to inform users on what the interface does or how to interact with it (e.g. a bin as a delete icon)
    - Skeumorphism isn't too widely used anymore as it can be very graphics heavy to both display and create
      - Where it is used it tends to be more lightweight, with simpler icons representing rather than recreating real world objects
  - Flat design
    - This is a minimalist design that is very performant but tends to be less visually interesting
    - It features simple contrasting colour schemes with no 3D elements, shading, or glare
    - This was popularised by Windows 8 in 2012
    - Users found flat design to be too minimal; it conveyed information clearly but didn't make interactive elements obvious enough
  - Flat design 2.0
    - This builds on flat design to address some of the issues users found with it
    - Some 3D elements like shading were added to give depth to interactive elements
    - Some skeumorphic elements serving as a visual metaphor to convey meaning
    - Some animations are included to guide users' attention
    - The basics of flat design are still here but with added visual clues to guide users to what they can interact with
  - Material design
    - This is Google's well-documented design system that is based in flat design 2.0
    - The priority here is on efficiency, performance, and good UX
    - The best examples of this come from any Google site using bold contrasting colours, icons, and consistent spacing
    - The 3 main parts of material design are:
      - Foundations - Accessibility, layout, and interaction states
      - Styles - Colours, elevation, icons, animations, shape, and typography
      - Components - Splitting elements by purpose (e.g. action, containment, communication, navigation, and text input)
    - The benefits of material design are the extensive docs created and maintained by Google
    - The drawbacks are that we're working within an existing framework so we need to study the docs, learn the framework, and stay within its boundaries
  - Glassmorphism
    - This is a design methodology focused on layering elements and creating a 'glassy' appearance
    - Glassmorphism really took off with Windows 7 in 2009 but has also been used in some versions of iOS and macOS
    - The glass effect comes from elements being semi-transparent, blurred, and including borders or shadows to add depth
    - Muted colours are often used as backgrounds to complement the glass effect
    - To maintain accessibility with glassmorphism we need to pay attention to the contrast between text and background colours
    - Another accessibility consideration is ensuring that transparency doesn't hinder legibility
  - Neumorphism
    - This combines flat design with skeumorphism, with a limited colour palette and extruded 'plastic' for interactive elements
    - It's been criticised for being inaccessible due to poor contrast since the highlighting comes from borders and shadows
  - Claymorphism
    - This is similar to neumorphism in some ways but with round 3D elements that look bubbly and friendly
    - Some of the same problems as neumorphism with contrast coming from borders and shadows instead of contrasting colours
    - Often accompanied by icons and diagrams that look like they're made of clay, hence the name
  - Motion Design
    - This is minimalist and relies on contrast, 3D elements, and animations to direct users' attention
    - Very much a 'less is more' approach that keeps the UI simple and uses animations and bursts of colour sparingly
  - Alternative scrolling
    - Also known as 'scrolljacking', this is a design technique where the standard scrolling behaviour is overriden
    - Sometimes this means that we scroll vertically but the site scrolls along horizontally, other times it means that an animation occurs as we scroll down
    - People have mixed feelings on this one as it can be frustrating for users to have control taken away and their expectations subverted
  - Brutalism
    - Inspired by the architectural technique of the same name, brutalism favours function over form with flat UI, few colours, and 'clunky' text
    - This comes as a response to the perceived lightness and optimism of mainstream modern web design
    - It's simple and can make it easy to convey information to users without the need for decoration
- Colour (not a design methodology)
  - A recurring theme on all of these is colour use, mainly in terms of how we can ensure contrast when working to a certain methodology
  - We don't always need to build our own colour palette, and even the best colour palette generators and contrast checkers can't ensure that it will look good
  - Contrast is a good start but there are some tips in colour selection that we can follow to create a more natural and appealing UI:
    - Follow the 60-30-10% principle for colour usage
      - 60% for the main background colour
      - 30% for typography and graphics
      - 10% for the most important elements to draw attention
    - Use no more than 3 primary colours
    - Use different shades of the colours chosen and different shades of grey
    - Use shades of grey instead of black and white, but don't overuse them
    - Use tools to check contrast for accessibility
    - Keep colours soft and balanced to maintain harmony

### Relational Databases
- 

