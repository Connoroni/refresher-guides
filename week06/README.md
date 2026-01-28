# Week 6 Refresher Guide

<details><summary><h3>Setting Up a React Project with Vite</h3></summary>

- Create a React app using `npm create vite@latest` just like we would for a vanilla Vite app
  - After choosing a project name we're asked to select a framework and we should choose React
  - We'll now be asked to choose a variant and we should choose JavaScript
- We'll have to `cd` into the newly created directory and run `npm i` to install the packages that we're using
- The structure of the app looks very different to vanilla JavaScript for good reason
  - We have an `index.html` file but this only contains our metadata, a `root` div, and a `<script>` tag linking to a file called `main.jsx`
  - `main.jsx` is the very top of our React app, containing the createRoot function to render our whole app inside the `root` div
    - On line 4 we have `import App from './App.jsx'` which is importing a component called 'App' which will contain the rest of our JSX code
    - Notice that lines 7-9 look a lot like HTML but this is JSX, the language used in React which effectively combines HTML (actually XML) with JavaScript
  - Now have a look at `App.jsx`, this is a component which is the core building block of React
    - On line 7 we have a function declaration but this function returns JSX, which makes it a component function
    - We can export this function (done here on line 37 with `export default App`) which lets us import it into another .jsx file (in this case `main.jsx`) and use it similarly to an HTML tag
      - A more typical example of a component being used is on line 12 of `App.jsx` where `<Header />` is used, this is a component imported on line 5
- Running `npm run dev` we will see the template React site on localhost
</details>

<details><summary><h3>React Components</h3></summary>

- Components are the main building blocks of React, allowing us to split up and reuse our JSX instead of having one huge file containing all our code
- A component is created as a `.jsx` file that contains a component function, which is a function that returns JSX
  ```js
  function Header() {
    return (
      <header>
        <h1>The Title</h1>
      </header>
    )
  }
  ```
  - We need to export this function so that we can import it into another component, often the `App` component
    ```js
    export default function Header() {
      return //...
    }
    ```
    - We can use `export default function` to create a function and export it on one line, but it's also common to create the component function and have `export ExampleFunction` at the bottom of the file
    - The difference between `export` and `export default` is that we can only have one default export per file, and can import it elsewhere without destructuring
- It's convention to name both our component files and component functions with a capital letter to distinguish them from other files and functions
  - This is why it's important to keep other files and functions in camelCase so we don't get them confused with our components
- With a component created and exported we then need to import it into another component (often into `App.jsx`) then render it by using it (technically called instantiating it) within that component
  ```js
  import Header from "./components/Header";
  
  export default function App() {
    return (
      <>
        <Header/>
        <p>Here is some text!</p>
      </>
    )
  }
  ```
  - We can think about this as being like an HTML tag, in this case a self-closing tag like an `<img/>`
- Components aren't just for splitting up our code to make it tidy, they also let us easily reuse JSX just like functions let us reuse JavaScript
  ```js
  export default function App() {
    return (
      <Header/>
      <Header/>
      <Header/>
    )
  }
  ```
  - The above example will give three headers, maybe not the best use of reusing components
- For various reasons (usually CSS styling) we don't always want our components to be wrapped in an element like a div, but React forces us to have one parent element per component with everything else nested inside it so we need to use a fragment which looks like an empty HTML tag `<>`
  ```js
  export default function BodyText() {
    return (
      <>
        <h2>Subheading</h2>
        <p>Lorem ipsum something something...</p>
      </>
    )
  }
  ```
  - We need to do this because functions, even component functions, can only return one thing so we need one parent element even if it's a fragment
  - Now our `h2` and `p` elements will be nested inside whatever the component is nested in, with no fragment to be seen which can be conformed if we look in dev tools
- We can import components into other components to use them there, but ultimately everything ends up imported into `App.jsx` which is in turn imported into `main.jsx` where it is rendered inside the `root` div
</details>

<details><summary><h3>React Props</h3></summary>

- Props allow us to customise our components so that they aren't the same each time we use them, useful if we have something like an image component that we want to have different `src` an `alt` each time we use it
- If we think of components like HTML elements, then props are like attributes that can change things about our components
- We 'pass' props by setting them in the parent component each time we use the imported child component
  ```js
  import ImageAndLabel from "./components/ImageAndLabel"
  
  export default function App() {
    return (
      <>
        <ImageAndLabel
          label="A Lovely Cat"
          source="https://unsplash.com/photos/75715CVEJhI"
          alt="A fluffy orange cat with big ears and long whiskers"
        />
      </>
    )
  }
  ```
- The child component needs to accept props matching the ones we're passing to it, very similarly to using parameters in a function so we can pass arguments
  ```js
  export default function ImageAndLabel(props) {
    return (
      <div>
        <figure>
          <img src={source} alt={alt} />
          <figcaption>{label}</figcaption>
        </figure>
      </div>
    )
  }
  ```
  - Using {curly brackets} in JSX lets us use JavaScript instead of a string, so in this case that means it's getting the values of `label`, `source`, and `alt` instead of just using the words label, source, and alt
  - `props` is an object so we can access specific properties using dot notation like we would for any other object, or we can destructure it to access the properties directly
    ```js
    export default function ImageAndLabel({ label, source, alt }) {
      return (
        <div>
          <figure>
            <img src={source} alt={alt} />
            <figcaption>{label}</figcaption>
          </figure>
        </div>
      )
    }
    ```
- One of the main values of props is to let us reuse a component but give it different props each time
  ```js
  export default function App() {
    return (
      <>
        <ImageAndLabel
          label="A Lovely Cat"
          source="https://unsplash.com/photos/75715CVEJhI"
          alt="A fluffy orange cat with big ears and long whiskers"
        />
        <ImageAndLabel
          label="A Lovely Dog"
          source="https://unsplash.com/photos/w7lH4jLh9V0"
          alt="A border collie laying in the grass"
        />
        <ImageAndLabel
          label="A Lovely Orangutan"
          source="https://unsplash.com/photos/l7YaTZz-lbA"
          alt="An orangutan holding onto a tree branch"
        />
      </>
    )
  }
  ```
  - Now we have the same component three times but with different content each time through the power of props
</details>

<details><summary><h3>React Styling</h3></summary>

- React has many (3) options for styling and it comes down to preference, but some/all devs have strong feelings about their preferred method and the people who use other methods
- One tweak common to all these methods is that we have to use the `className` attribute instead of `class` to give our elements a class because we're working in JSX not HTML and class is a reserved word
- We can do our styling in one big CSS file like we would in vanilla JavaScript, with the only difference being that we import the CSS file into `App.jsx` or `main.jsx` instead of using a `<Link>` tag
  ```js
  import "./App.css"
  ```
  - By default we have `App.css` and `index.css` created by the Vite template, we can delete one of these and write our CSS in the other if we want to use this method
- Just like we split up our JSX into separate component files we can also split up our CSS for each component then import them into the relevant component file
  ```js
  // Header.jsx
  import "./Header.css"
  
  export default function Header() {
    return (
      <header>
        <h1 className="header-title">Here's the Title</h1>
        <img
          src="/logo"
          className="logo-img"
        />
      </header>
    )
  }
  ```
  ```css
  /* Header.css */
  .header-title {
    color: #fff;
    padding: 1rem;
    text-decoration: underline;
  }
  ```
  - We can't use tag selectors in CSS to target just the elements in the desired component because the component will be imported into `App.jsx` and take the imported CSS file with it, meaning every instance of that tag would be selected across all components
- The third method is to use [Tailwind CSS](https://tailwindcss.com/), a CSS framework that lets us handle styling inline through the className attribute with shorthands and presets without needing a separate CSS file
  - We need to install tailwindcss and @tailwindcss/vite through npm `npm install tailwindcss @tailwindcss/vite`
  - Next we go to our `vite.config.js` file to import tailwind and make it available to our React app
    ```js
    import { defineConfig } from "vite";
    
    import tailwindcss from "@tailwindcss/vite";
    
    export default defineConfig({
      plugins: [tailwindcss()],
    });
    ```
  - Finally we go to our global CSS file (either `App.css` or `index.css`) and at the top of the file we add
    ```css
    @import "tailwindcss";
    ```
  - Now Tailwind is installed and we can use it in our app
</details>

<details><summary><h3>Tailwind CSS</h3></summary>

- Tailwind is very different to writing normal CSS
- We don't have reusable classes and selectors or even a CSS file, we write our styles in the className attribute for each element
- We use shorthand called utility classes for each CSS property in the className e.g. the two examples below do the exact same but one is written in Tailwind
  <details><summary>Vanilla:</summary>

  ```css
  .containerDiv {
    background-color: #ef4444;
    display: flex;
    flex-direction: column;
  }
  ```
  </details>
  <details><summary>Tailwind:</summary>

  ```html
  <div className="bg-red-500 flex flex-col">
  ```
  </details>
  - Both examples above will style our div with a red background and arrange the elements inside it with flexbox in a column
- A lot of Tailwind comes down to remembering the utility classes that correspond to the styles we want to apply, and the best method to do this is checking the Tailwind docs at [https://tailwindcss.com/docs](https://tailwindcss.com/docs) where we can search for properties to check the shorthand and the different values we can use
- While some properties use a single word (like `flex`) most still need a value for things like size or colour, and we have a few options for these
  - We can use preset values that we can check in the docs e.g. for setting `border-radius` we have options like `rounded-sm` or `rounded-full`
  - Some elements use numbers e.g. to set `width` we can use `w-1` to mean `0.25rem` so `w-4` is `1rem`, `w-8` is `2rem`, etc.
  - We can use arbitrary values in square brackets to set our own values for properties if there isn't an existing utility class e.g. for setting custom `width` values we can use `w-[50vw]` or `w-[200px]`
- We can use media queries very easily in Tailwind by prefacing a utility class with a screen size breakpoint like `sm`, `md`, `lg`, `xl`, or `2xl`
  ```html
  <div className="flex flex-col md:flex-row">
  ```
  - These media queries apply to a certain screen size and above, so the styling is mobile-first by default and overwritten by any active media queries
- We can use pseudo-classes like `:hover` and `:focus` in Tailwind similarly to media queries by prefacing the style we want to apply with the name of the pseudo-class
  ```html
  <button className="rounded-full bg-white hover:bg-black hover:text-white">Click Me!</button>
  ```
- There are a couple of very handy VS Code extensions for using Tailwind
  - [Tailwind Intellisense](https://marketplace.visualstudio.com/items/?itemName=bradlc.vscode-tailwindcss) gives a huge list of autocomplete options when writing Tailwind that we can scroll through to view all our options
  - [Tailwind Fold](https://marketplace.visualstudio.com/items/?itemName=stivo.tailwind-fold) collapses our classes which can be very handy in bigger projects where every element has many classes
</details>

<details><summary><h3>React Events</h3></summary>

- As JSX combines both HTML and JavaScript in one file, we don't need to select elements and use `addEventListener` and instead add the event as an attribute to the element
- The attributes we use are all 'on something' so the most common is `onClick` but we also have `onSubmit` and the whole suite of event listeners that are available in vanilla JavaScript
  - We pass a function to the event attribute which is the event handler
    ```js
    export default function NiceButton() {
      function buttonAlert() {
        console.log("Button clicked!")
      }

      return (
        <button onClick={buttonAlert}>Click Me!</button>
      )
    }
    ```
- Like with any function we can use arrow functions to create event handlers inline if we don't need to reuse them
  ```js
  export default function NiceButton() {
    return (
      <button onClick={() => {console.log("Button clicked!")}}>Click Me!</button>
    )
  }
  ```
  - We can also use arrow functions to pass arguments to the event handler which we can't normally do without invoking the function
  ```js
  export default function OtherButton() {
    function clickHandler(message) {
      console.log(message)
    }
    return(
      <button onClick={() => {clickHandler("I'm passing arguments from an inline event listener! Wow!")}}>Click This Please</button>
    )
  }
  ```
- To make our components even more reusable we can pass event handlers as props, allowing our components to invoke different functions from the same input
  ```js
  {/* App.jsx */}
  
  import PropButton from "./components/PropButton"
  
  export default function App() {
    function sayHello() {
      console.log("Hello")
    }
    function sayHi() {
      console.log("Hi")
    }
    function sayHeyUp() {
      console.log("Hey up")
    }
  
    return(
      <>
        <PropButton event={sayHello} />
        <PropButton event={sayHi} />
        <PropButton event={sayHeyUp} />
      </>
    )
  }
  ```
  ```js
  {/* PropButton.jsx */}
  
  export default function PropButton({ event }) {
    return (
      <button onClick={event}>Click me for a greeting!</button>
    )
  }
  ```
  - Now we have three buttons all using the same component but triggering a different event each time through the power of props
</details>

<details><summary><h3>The useState Hook</h3></summary>

- State is a very important concept to React, used to store data that will change over time and re-render the component when state changes
- Hooks are special functions built into React that we can use to easily make use of React functionality, and useState is one of these hooks
  - You can spot other hooks because they all start with 'use' e.g. `useState`, `useEffect`, `useRef`, and `useMemo`
- To use state in a component we need to first import it then use it by destructuring the array that it returns
  ```js
  import { useState } from "react";
  
  export default function App() {
    const [colour, setColour] = useState("red")
  }
  ```
  - There are three elements that we set when creating a new state: initial state, state variable, and mutation function
    - Initial state is the argument (in brackets) passed to useState and is the initial value of our state variable
    - The state variable is the variable that our current state is stored in and can be referenced in our code just like any other variable e.g. `console.log(colour)` will log the current value of our colour state
    - The mutation function is needed to alter/mutate the value of our state as we can't alter the value of state directly e.g. we can't use `counter = counter + 1` but we can use `setCounter(counter + 1)`
- Here's an example of using the colour state we created above to control the background colour of a div using multiple buttons
  ```js
  import { useState } from "react";
  
  export default function App() {
    const [colour, setColour] = useState("red")
    
    return (
      <>
        <div className={colour}>
          <p>The current colour is {colour}</p>
        </div>
        <button onClick={() => {setColour("red")}}>Make it red!</button>
        <button onClick={() => {setColour("blue")}}>Make it blue!</button>
        <button onClick={() => {setColour("green")}}>Make it green!</button>
      </>
    )
  }
  ```
  - For the above example we'd need to create classes in CSS with styling to give the `div` a background colour for each class
    - We could instead use Tailwind with a template literal to skip this step (because Tailwind has colours called red, blue, and green but this wouldn't work for everything)
      ```js
      <div className=`bg-${colour}-500`>
        <p>The current colour is {colour}</p>
      </div>
      ```
</details>

<details><summary><h3>Conditional Rendering</h3></summary>

- One thing that React makes easy for us is conditional rendering, meaning to show or hide parts of the UI based on some condition
  - Usually this is done by changing state or changing the props passed from a parent component as these are the two things that trigger a re-render
- There are two operators we can use to handle conditional rendering: an `AND` operator (`&&`) or a ternary operator
- Using the `AND` operator allows for conditional rendering because it returns the second value only if the first is truthy
  ```js
  import { useState } from "react";
  
  export default function App() {
    const [isVisible, setVisible] = useState(true)
  
    return (
      <>
        {isVisible && <p>I am here, but only sometimes</p>}
        <button onClick={() => {setVisible(!isVisible)}}>Toggle visibility</button>
      </>
    )
  }
  ```
  - This works fine when the value is `false` but not when it is a falsy value such as 0, as in the case of using `array.length` with an empty array
    ```js
    import { useState } from "react";
    
    export default function App() {
      const [numbersArray, setNumbersArray] = useState([])
    
      return (
        <>
          {numbersArray.length && <p>I am here, but only sometimes</p>}
          <button onClick={() => {setNumbersArray([0, 1, 2, 3, 4])}}>Add some numbers to the array</button>
        </>
      )
    }
    - The `&&` operator always returns the first falsy value or the value on the right if both are truthy, in this case `numbersArray.length` is falsy when the length is `0` so the operator returns `0` and renders it
      - We don't have this issue for `false` because it is considered the same as `null` or `undefined` in React so nothing is rendered, but falsy values don't get this treatment so they get rendered
- The ternary operator is the only operator in JavaScript that takes three values: a condition, one value to return if the condition is truthy, and another to return if the condition is falsy
  ```js
  import { useState } from "react";
  
  export default function App() {
    const [isVisible, setVisible] = useState(true)
  
    return (
      <>
        {isVisible ? <p>I am here, but only sometimes</p> : null}
        <button onClick={() => {setVisible(!isVisible)}}>Toggle visibility</button>
      </>
    )
  }
  ```
  - The condition here is `isVisible` and is followed by a `?`
  - The first clause after the `?` is the value returned if the condition is truthy
  - The clause after the `:` is the value returned if the condition is falsy, in this case it's null as we don't want anything to render here
- The ternary operator avoids the problem of rendering unwanted values like the `&&` operator because we specify exactly what value to return in each case
- In summary, the ternary operator is always safe while the `&&` operator is fine as long as the left value can't be 0 or another falsy value that isn't `false`
</details>

<details><summary><h3>Lists and Keys</h3></summary>

- React makes it easy to loop through an array and render elements (or even entire components) for each item in the array
- We use the `.map` method which returns a new array containing the results of running a callback function on each item in the original array, quite similar to the `.forEach` method
  ```js
  export default function App() {
    const numberArray = [5, 19, 25, 17, 42]
    const doubledArray = numberArray.map((number) => <li>{number * 2}</li>);
  
    return (
      <ul>{doubledArray}</ul>
    )
  }
  ```
  - Just like `.forEach` we pass each element in the array as an argument to the callback function which then returns an array with a JSX element generated from the array element
- More often in React we'll see a `.map` done inline in our returned JSX which works just as well in less lines
  ```js
  export default function App() {
    const numberArray = [5, 19, 25, 17, 42]
  
    return (
      <ul>
        {numberArray.map((number) => <li>{number * 2}</li>)}
      </ul>
    )
  }
  ```
- We get an error if we use `.map` without passing a `key` prop to the parent element which is a unique identifier for each item in the list, similar to a `PRIMARY KEY` used in a database
  ```js
  export default function App() {
    const userArray = [
      {
        id: 1, name: "Bob",
      },
      {
        id: 2, name: "Susan",
      },
      {
        id: 3, name: "Bort",
      },
      {
        id: 4, name: "Jim",
      },
    ]
  
    return (
      <>
        {userArray.map((user) => (
          <div key={user.id}>
            <p>{user.name}</p>
          </div>
        ))}
      </>
    )
  }
  ```
  - It might be tempting to use the index as the key but this wouldn't be appropriate as it would mean the key could change between each render if an element was removed from the array and cause the entire array to re-render as the key would change
  - In this case we've made each item in the array into an object so we can include an `id` property to use as the key, ensuring that it's unique for each item
- Just like in our component functions we can only return one thing so we need to wrap all of our returned JSX in a single parent element
</details>

<details><summary><h3>The useEffect Hook & Fetch in React</h3></summary>

- useEffect is another common React hook that we'll use to run side effects, meaning anything we want to do that isn't related to rendering the UI
- In general useEffect is used to connect to external systems not controlled by React including APIs and timers
- The useEffect hook takes two arguments: a callback function and a dependency array
  - The callback function runs every time one of the values in the dependency array changes, or if we leave it empty then it only runs when the component renders
- To fetch data from an external source in React we'll use useEffect and useState together so we can store the fetched data in state
  ```js
  import { useEffect, useState } from "react";
  
  export default function App() {
    const [posts, setPosts] = useState([]);
    
    useEffect(() => {
      async function fetchData() {
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/posts"
        );
        const data = await response.json();
        setPosts(data);
      }
      fetchData();
    }, [])
    
    return(
      {/* ... */}
    )
  }
  ```
  - The function needs to be async because we're fetching data but the callback function passed to `useEffect` can't be async, so we use an arrow function and declaring a function within it then call that function
  - The dependency array here is empty so the effect is run once when the component is rendered
- Often the data we're fetching comes in the form of an array so we'll want to map through the data once it's stored in state
- If our data is an object or an array of objects we can get an error when we try to use our state before assigning our fetched data to it which will happen when the component renders but before the async function in the effect has returned any data
  - We can use a ternary operator to avoid this
    ```js
    import { useEffect, useState } from "react";
    
    export default function App() {
      const [posts, setPosts] = useState([]);
      
      useEffect(() => {
        async function fetchData() {
          const response = await fetch(
            "https://jsonplaceholder.typicode.com/posts"
          );
          const data = await response.json();
          setPosts(data);
        }
        fetchData();
      }, [])
      
      return(
        <>
          {posts.length ? (
            posts.map((post) => (
              <div key={post.id}>
                <h2>{post.title}</h2>
                <p>{post.body}</p>
              </div>
            ))
          ) : <p>Loading...</p>
          )}
        </>
      )
    }
    ```
  - Alternatively we can use something called optional chaining (sticking a ? in our dot notation) which lets us get a property from an object only if that property exists instead of throwing an error
    ```js
    import { useEffect, useState } from "react";
    
    export default function App() {
      const [posts, setPosts] = useState([]);
      
      useEffect(() => {
        async function fetchData() {
          const response = await fetch(
            "https://jsonplaceholder.typicode.com/posts"
          );
          const data = await response.json();
          setPosts(data);
        }
        fetchData();
      }, [])
      
      return(
        <>
            {posts.map((post) => (
              <div key={post?.id}>
                <h2>{post?.title}</h2>
                <p>{post?.body}</p>
              </div>
            ))}
        </>
      )
    }
    ```
  - Which of these solutions we want to use depends on the structure of the data we're getting and how we want to use it
</details>
