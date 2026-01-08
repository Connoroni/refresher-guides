# Week 3 Refresher Guide

<details><summary><h3>Forms, User Input, and Validation Feedback</h3></summary>

- Forms are a crucial part of many websites as they are how we accept user inputs that are more complex than just a click or a hover 
- A form is an HTML element that contains input elements in which users can input data, but we should also include label elements to tell users what the input is for:
  ```html
  <form>
    <label for="username">Username:</label>
    <input type="text" name="username" />
    <label for="age">Age:</label>
    <input type="number" name="age" />
  </form>
  ```
- A ```<form>``` on its own won't do anything if we don't have a way to submit it, so we also need to include a ```<button>``` nested inside it and we add the ```type="submit"``` attribute to the button:
  ```html
  <form>
    <label for="username">Username:</label>
    <input type="text" name="username" />
    <label for="age">Age:</label>
    <input type="number" name="age" />
    <button type="submit">Submit the form</button>
  </form>
  ```
- The type attribute on an ```<input>``` element determines the type of data the user can input, there are too many options to go over here but here are some examples on MDN - https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms/HTML5_input_types
- Another useful attribute is required which is used to ensure that an input field has something in it so we can differentiate between fields that are optional and those that aren't
- On the JavaScript side of things submit is an event that we can create an event listener and handler for like any other event
  - Remember: The submit event needs to be attached to the form itself, not the button or any of the inputs!
- There are a few steps we need in a submit event handler function:
  - Give it a parameter of event (often abbreviated to e)
  - Use the `event.preventDefault()` method to prevent the default behaviour of a submission which is to add all the inputted data to the url
  - Create a `FormData` object from the inputted data
    - Remember: `FormData` creates an object from the input fields and inputted data, but is formatted differently from a normal js object so we can't leave the data like this
  - Use the `Object.fromEntries()` method to turn the `FormData` object we created into a nice normal object like we're used to
- Put it all together and it should look something like this:
  ```js
  const myForm = document.getElementById("myForm");
  
  myForm.addEventListener("submit", (event) => {
    event.preventDefault();
    const formData = new FormData(myForm);
    const niceObject = Object.fromEntries(formData);
  });
  ```
- Now we can use the values in the object we created just like we would use any other object, getting individual properties with dot notation
</details>

<details><summary><h3>Fetch and APIs</h3></summary>

- API stands for Application Programming Interface which is a very vague term that can refer to any connection between different computers or programs, but what we're interested in are web APIs which are APIs for servers
- We often want our apps to get data from an API that stores the data for us so we don't have to include it in the app ourselves
  - This is especially useful in apps that feature user submitted data whether that's social media posts, comments, reviews, or anything else as it would be impossible to hardcode these in the app
- The tool JavaScript provides to get data from an API is the `fetch()` method which lets us send HTTP requests to web APIs
  - Remember: Cast your mind back to when we discussed the Web Request-Response Cycle, this is what we're doing with fetch as we send HTTP requests and get responses
- Because fetch interacts with an external source it won't have the data we want instantly (the time it takes will vary across devices and download speeds) and will instead return a promise, so we need to use it in an async function
  - We need to use the `await` keyword to tell our code to pause execution until we have data that isn't a promise, but we can only do this inside an async function which we declare using the `async` keyword:
    ```js
    async function getSomeData() {
      const response = await fetch("https://thisapi.com/somedata");
      console.log(response);
    }
    ```
  - The response we get from fetch is a response object containing the body often in the form of JSON (JavaScript Object Notation) which looks like an object but is actually just a string so we need to run the `array.json()` method to convert it into an actual object so we can use the data stored within:
    ```js
    async function getSomeData() {
      const response = await fetch("https://thisapi.com/somedata");
      const jsonResponse = await response.json();
      console.log(jsonResponse);
    }
    ```
    - Also in the response object is a HTTP status code telling us the status of the response; common ones include `200: OK`, `404: Not Found`, and `500: Internal Server Error`
</details>

<details><summary><h3>Dev Tools: Network Tab</h3></summary>

- The network tab in dev tools shows all the requests the browser is making including page load, fetching data, and loading images
- We also have a throttling option that lets us simulate worse internet connections to see how a page loads which is useful for seeing things like how long images take to load or how the page looks while we wait for data to be fetched
- The main use of the network tab is to look at our requests for debugging so we can see properties like the request body, status code, etc.
</details>

<details><summary><h3>JavaScript Under the Hood</h3></summary>

- Some useful definitions:
  - Execution Context: An environment created for our code to be executed in
  - Call Stack: A queue of all the tasks for the browser (or Node.js) to execute, works on 'last in, first out'
  - Microtask Queue: A queue for asynchronous tasks involving promises (e.g. fetch or .json), checked after the Call Stack
  - Callback Queue: A queue for tasks performed by Web APIs (e.g. setInterval, setTimeout), checked after both the Call Stack and Microtask Queue
  - Web APIs: These handle asynchronous tasks from the browser including timers and fetch requests
  - Event Loop: The whole process of checking the stacks and moving tasks between them
- JavaScript is a single-threaded language which means it can only execute one task at a time
- While JavaScript can only do one thing at a time, we don't want to wait for that task so we have a few different queues that tasks can get added to
- Our js file is executed line by line from top to bottom but execution is split up into different execution contexts
  - We start with a global execution context that runs the whole js file, but when it encounters a function call it will create a new function execution context where that function is run
  - A new execution context is created for each function and any variables declared within that function are only stored in memory while that execution context is active
- We know what happens as our file is read, but to understand *how* it happens we need to understand the Call Stack
  - The Call Stack is a queue of all the tasks in our code that the browser needs to execute and it works on a 'last in, first out' system meaning the most recent task to be added will be the first to be completed and removed from the stack
- That's all well and good but we also know about some tasks that don't conform to the standard order of execution like timers and asynchronous code, so what happens there?
  - There are some extra queues for tasks like these that can't go in the standard Call Stack: the Callback Queue and the Microtask Queue
  - The idea here is that we don't want all our other tasks to have to wait for these tasks which could take a long time, so we stick them on these other queues that only get checked once the Call Stack is empty
  - The Callback Queue is for tasks handled by Web APIs including timers
    - Timers (both `setInterval` and `setTimeout)` go on the Callback Queue, but because they're waiting for the Call Stack to be empty other tasks will continue to be run until both the Call Stack is empty and the time specified in the timer has passed
  - The Microtask Queue is for asynchronous tasks that involve promises including fetch, but these have a higher priority than tasks on the Callback Queue so they run after the Call Stack is cleared but before the Callback Queue is checked
    - Just like with timers, we don't want all the tasks on our Call Stack to have to wait for the result of fetching data, so the fetch execution context goes on the Microtask Queue while it waits for the the response to arrive and the Call Stack to be clear
</details>

<details><summary><h3>Design Principles</h3></summary>

- It's all well and good to make an app that works perfectly and has some super advanced features, but nobody will want to use it if it's ugly and inconvenient to use
- There are 10 principles we should be aware of, but not every project needs to adhere strictly to all of them:
  - Type of site
    - Knowing the type/purpose of the site we can decide what actions a user should be able to take and what should be immediately visible to them
    - If we want the user to search then we should have a very visible search bar, if we want them to read articles we should have select articles visible
  - User flow
    - We have to think like the users of our site and how they will "flow" through the site, what they will interact with and where they will navigate to etc.
    - Users will also have expectations for how to navigate through the site informed by other similar sites and general conventions
    - The idea here is to steer users through the site to reach the point we want them to reach
    - We can plan this out as part of our wireframe, considering how users will reach the point we want them to reach and how we can make this clear 
  - Hierarchy
    - Hierarchy is all about the order of importance of elements on the page
    - Similarly to user flow we should think about what we want users to interact with and make it clear to them which elements are the most important
    - Some of the key ways we can define hierarchy are:
      - Decisiveness - making decisions about what the user should see and should focus on
      - Contrast - using contrasting colours and shapes to draw the user's eye and differentiate interactive elements
      - Emphasis - draw the user's attention with size, positioning, bold text, different colours, arrows, etc.
      - Visual hierarchy - using emphasis to dictate the order in which the user reads the information on the page
      - White space - helpful to calm the UI to make the UX more pleasant, gives some breathing room and reduces cognitive load
  - Consistency
    - Consistency is important for reducing the cognitive load on the user
    - We should maintain harmony between elements by keeping consistency in things like colour scheme, font spacing, and font size
    - This is made a lot easier by having reusable components or guidelines to ensure consistent styling between the same type of elements in different places, e.g. having all subheadings styled the same, always using bullet points for lists, etc.
  - Balance
    - Balance is all about balancing content with white space which reduces cognitive load, makes the site more appealing to look at, and helps emphasise the content we want the focus to be on
    - Part of this is having content nicely centered and not having more content around what we want the user to focus on
    - As with everything we want to direct the user towards the elements we want them to interact with, and using white space instead of clutter helps a lot with this
  - Simplicity
    - Simplicity is all about reducing the cognitive load on the user by making things as straightforward as possible for them
    - Chunking is useful here, meaning that we keep related UI elements together such as all navigation in the header or all contact information in the footer
    - In things like carousels or large lists we want to show only up to 7 elements at a time to reduce cognitive load; an example is on shopping sites like Amazon or eBay that don't want to show us every item at once
  - Contrast
    - Contrast is helpful for both accessibility and emphasis
    - High contrast is very helpful for colourblind users but also helps define important elements and makes our site more easily readable
    - Contrast takes more effort than some of the other design principles because we have to learn what's good and bad, think how it affects our site, and implement it in a way that fits with our branding and theme
    - The Adobe Contrast Checker is helpful for analysing the contrast between two colours as it gives us a score - https://color.adobe.com/create/color-contrast-analyzer
  - Imagery
    - Imagery should be used to enhance the app or page, and is useful for everything from advertising products to breaking up text
    - Sometime images are the content, for example if we have cards with images like for products on an online store
    - We need to make decisions about the images we use:
      - High or low resolution
      - What is in focus?
      - Lighting
      - Cropping
      - Colour grading
      - Retouching
      - Customising the image
    - For everything on the course we can use any images we want, but for production apps we need to consider what images we're allowed to use
      - We can use our own work freely, unsplash is good for stock images, and any other images with open source or the right license
  - Typography
    - Typography is how we style and arrange our type/font, and a lot of it ties into consistency
    - Don't have:
      - Too many different fonts
      - Too much variation in colour
      - Overuse of bold and italics
    - All of these things should be used for emphasis, so overusing them makes the site messy and unclear with the user not knowing which elements to focus on
    - Do have:
      - Neat alignment and spacing
      - Variation in scale (to communicate hierarchy and emphasis)
      - Good contrast (more readable and accessible, and provides emphasis)
      - Consistent colours
      - Sensible choice of font
  - User Feedback
    - It is important to give users feedback to let them know when they've done something/when something is happening
    - We can use HTML attributes like disabled, and CSS pseudo-selectors like :hover, :active, :focus, and :default to add some simple user feedback
      - Some of these like :focus also improve accessibility by giving feedback for navigation using TAB as well as the cursor
    - We should use signposts to let our user know where they are and how they got there, such as highlighting the active tab or having clear page titles
- The idea behind these isn't to create the nicest looking site in the world, but to make it easy for users to interact with so that they can do what we want them to do on our site
</details>

<details><summary><h3>Debugger</h3></summary>

- You've probably encountered errors or unexpected results from your code by now, which is why it's important to be able to debug our code
- We often debug by using the `console.log()` method to check the values of different variables or display a message as part of a function so we can be sure that it's running as expected
- The debugger is an alternative tool that we can use for debugging by placing it in our code to pause execution in the browser and allow us to step through the code
  ```js
  let myVariable = 1;
  
  function myFunction() {
    debugger;
    myVariable = 5;
  };
  
  myFunction();
  ```
- When we execute the code containing the `debugger;` statement execution pauses and we can use the sources tab to inspect the values of each of our variables at this point in time to make sure they're what we expect
- We can also step through our code in dev tools with the "step over next function call" button in the sources tab which goes through our code one line at a time, letting us check current values at each step
</details>

<details><summary><h3>NPM Project Setup</h3></summary>

- NPM (Node Package Manager) is a tool that lets us install packages, by which we mean third party code to be used in our app
- To initialise NPM in a project we run ```npm init -y``` in the terminal which creates a `package.json` file
  - `package.json` contains information on our project including the installed packages and any scripts we can run
- To install a package we run ```npm install <package-name>``` which we can abbreviate to ```npm i <package-name>``` if we want to be lazy/efficient
  - Running ```npm install``` or ```npm i``` without a package name will install all the packages a project requires which are read from `package.json`
- Installing a package will create a node_modules folder which contains all the code for the package, but there will probably be more packages than those we installed because we also install any dependencies of our installed packages (packages that are required by the installed packages)
- We will also have a package-lock.json, a file similar to our `package.json` that contains more details on all the installed packages, but we never need to change anything in here
- To be able to use an installed package we need to import it and there are two ways to do this:
  - The CommonJS way uses the line ```js const *package* = require("*package*") ``` e.g. ```js const cowsay = require("cowsay")```
  - The modern ES6 way uses the line ```js import *package* from "*package*" ``` e.g. ```js import cowsay from "cowsay"```
    - To do this we need to add a line in our package.json first to enable this: ```"type": "module",```
      - This can go anywhere in the file as long as it's only within the main object and not within any of the other objects
- Inside `package.json` is a "scripts" object that contains scripts we can run through npm, and it's a common convention to add a "dev" script here to let us run our app in development mode 
  ```js 
  "scripts": {
    "dev": "node app"
  }
  ```
  - Now instead of running `node app` (or whatever js file we want to run) in the terminal we can run ```npm run dev``` and this will run the dev script
    - This may not sound like a time saver but it's worth getting used to because other frameworks and tools we will cover use a dev script to do more complex tasks than running ```node app```
- Our node_modules folder with all the files of our dependencies can get pretty big so we don't want to push it to Github with the rest of our project, which we do by creating a `.gitignore` file
  - In the `.gitignore` file we just add `node_modules` (and any other files or folders that we don't want to be pushed to Github)
  - Now when someone clones our repo from Github they just need to run ```npm install``` or ```npm i``` to install all the packages and get the `node_modules` folder that we didn't push
</details>

<details><summary><h3>Unit Testing with Vitest</h3></summary>

- Unit testing is the testing of small pieces of code in isolation such as a single function
- Unit testing is made more difficult by using global variables as our tests won't have access to these, so we often want to avoid global variables if possible
- We can make a test file such as `app.test.js` (assuming our main js file is app.js) where we can import our functions from the file they were written in e.g. ```js import {isPalindrome, toTitleCase} from "./app"; ```
  - To be able to import these functions in the test file we need to first export the functions from the file they're written in e.g. 
    ```js
    export function isPalindrome(string) {
      //function goes here
    }
    ```
- Vitest is a package we can use to run these tests for us but first we need to import some functions from the package ```import { test, expect, describe } from "vitest";```
- We use these to create individual tests for our functions and tell us whether the test outputs the expired result or not
- `describe()` takes a string and a callback function as arguments and the callback function should include `test()` which also takes a string and a callback function as arguments so we can give a label to the whole block of tests and each individual test:
  ```js
  describe("Words that are palindromes", () => {
    test("racecar", () => {
      const result = isPalindrome("racecar");
      expect(result).toBe(true);
    });
    test("madam", () => {
      const result = isPalindrome("madam");
      expect(result).toBe(true);
    });
  });
  ```
  - We use the result returned by our function as an argument of `expect()` and use the `.toBe()` method to compare the result to the expected result
- We run the tests using ``` npx vitest ``` or we can create a test script in our package.json so we can do the same with ```npm run test```
  ```
  "scripts": {
    "test": "vitest"
  }, 
  ```
  - Running the above command will give the following output with the tests we wrote before because both are correct
    ```
    ✓ app.test.js (2 tests) 2ms
      ✓ Words that are palindromes > racecar 1ms
      ✓ Words that are palindromes > madam 0ms

    Test Files  1 passed (1)
    Tests  2 passed (2)
    ```
  - If we add a test where the result doesn't match the expected result we will get more details telling us which test(s) failed and why
    ```
    ❯ app.test.js (3 tests | 1 failed) 6ms
      ✓ Words that are palindromes > racecar 1ms
      ✓ Words that are palindromes > madam 0ms
    × Words that are palindromes > word 4ms
      → expected false to be true // Object.is equality

    ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

     FAIL  app.test.js > Words that are palindromes > word
    AssertionError: expected false to be true // Object.is equality

    - Expected
    + Received

    - true
    + false

     ❯ app.test.js:52:20
         50|   test("word", () => {
         51|     const result = isPalindrome("word");
         52|     expect(result).toBe(true);
           |                    ^
         53|   });

    ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


    Test Files  1 failed (1)
         Tests  1 failed | 2 passed (3)
    ```
  - Since this test returns a boolean, instead of checking that certain inputs fail the test we can create a new test for inputs that will return false
    ```js
    describe("Words that are NOT palindromes", () => {
      test("Connor", () => {
        const result = isPalindrome("Connor");
        expect(result).toBe(false);
      });

      test("Word", () => {
        const result = isPalindrome("Word");
        expect(result).toBe(false);
      });

      test("Some other words", () => {
        const result = isPalindrome("Some other words");
        expect(result).toBe(false);
      });
    }); 
    ```
</details>

<details><summary><h3>Timers</h3></summary>

- Timers let our code interact with time, allowing us to run functions either at regular intervals or after a certain amount of time has passed
- There are two types of timers in JavaScript created by the ```setInterval()``` and ```setTimeout()``` methods
- Both types of timers take a callback function and a number in milliseconds as arguments 
- setInterval() runs the function every time the specified amount of time passes while setTimeout() runs the function once when the time passes
  - This timeout will log ```"Hello world!"``` once after 5 seconds
    ```js
    setTimeout(() => {
      console.log("Hello world!");
    }, 5000);
    ```
  - This timer will log ```"Hello world!"``` every 5 seconds
    ```js
    setInterval(() => {
      console.log("Hello world!");
    }, 5000);
    ```
- We can clear an interval with ```clearInterval()``` to stop it from running but first we need to create a reference to the interval by assigning it to a variable
  ```js
  const myInterval = setInterval(() => {
    console.log("This is my interval. This message will repeat every second.");
  }, 1000);

  clearInterval(myInterval);
  ```
  - The problem here is that the interval will be cleared almost instantly as clearInterval() will run right after the interval is created, but we can use clearInterval() inside an event or even a timeout to clear the interval after a certain amount of time or upon user interaction
    ```js
    setTimeout(() => {
      clearInterval(myInterval);
    }, 5000);
    ``` 
</details>

<details><summary><h3>Local Storage</h3></summary>

- There are multiple ways to store users' data and we don't always need to build a database
- For smaller amount of data (<5MB) we can use Local Storage, often for things like user preferences on themes or toggleable content
- The main advantage of using Local Storage is that the data persists between refreshes and reboots
- Data is stored in local storage in key-value pairs similar to a JavaScript object
- We can check what is stored in Local Storage in the Application tab of dev tools where we can see Local Storage under the Storage heading
- The syntax for storing and retrieving data in Local Storage is simple:
  - `localStorage.setItem()` will store a key-value pair in Local Storage when given the key and value as arguments
    ```js
    const form = document.querySelector("form");
    
    form.addEventListener("submit", function (event) {
      event.preventDefault();
    
      const formData = new FormData(form);
      const colour = formData.get("colour");
    
      localStorage.setItem("colour", colour);
    });
    ```
  - ```localStorage.getItem()``` will retrieve the value from local storage when given the key as an argument
    ```js
    const colour = localStorage.getItem("colour");
    
    if (colour) {
      const input = document.querySelector("input");
      input.value = colour;
    }
    ```
- Local Storage can only accept strings, so if we want to store an object we will have to convert it to JSON using ```JSON.stringify()```
  ```js
  const form = document.querySelector("form");
  
  function savePreferences(event) {
    event.preventDefault();
  
    const formData = new FormData(form);
    const colour = formData.get("colour");
  
    // preferences is now an object, it might contain other preferences...
    const preferences = {
      colour,
    };
  
    // so when we save it, we stringify it
    localStorage.setItem("preferences", JSON.stringify(preferences));
  }
  
  form.addEventListener("submit", savePreferences);
  ```
  - Likewise, we have to parse the JSON back into an object when we retrieve it which we do with ```JSON.parse()```
    ```js
    const preferences = JSON.parse(localStorage.getItem("preferences"));
    
    if (preferences) {
      const input = document.querySelector("input");
      input.value = preferences.colour;
    }
    ```
- We can use the `||` operator with Local Storage values to get a value from Local Storage if it exists or a default value if it doesn't
  ```js
  const favColour = localStorage.getItem("colour") || #0000FF; 
  ```
  - This works because the `||` operator will return the value on the left if it is truthy or the value on the right if the left value is falsy
- We can remove an item from Local Storage using ```localStorage.removeItem()```
  ```js
  const clearButton = document.getElementById("clearButton");
  
  clearButton.addEventListener("click", () => {
    localStorage.removeItem("preferences");
  });
  ```
- We can listen for changes in Local Storage values using the "storage" event listener and several properties of the storage event object
  - This event listener is attached to the window, but it can only listen for changes to Local Storage made by a window other than itself, e.g. a different tab open on the same website
  ```js
  window.addEventListener("storage", handleLocalStorageChange);
  
  function handleLocalStorageChange(event) {
    if (event.key === "favouriteColour") {
      const newValue = event.newValue;
      console.log(`Local storage favouriteColour changed to: ${newValue}`);
    }
  }
  ```
</details>
