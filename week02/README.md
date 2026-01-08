# Week 2 Refresher Guide

You're two weeks into the course now and you've now had the opportunity to sink your teeth into some JavaScript. Here's a refresher of everything you've learned this week to refer back to later.

<details><summary><h3>JS Arrays</h3></summary>

- Arrays are a data type in JavaScript that can store multiple pieces of data in a list
- We create an array the same way we declare any variable but use the syntax of a list in square brackets separated by commas e.g.
  ```js
  const myArray = ["item1", "item2", "item3"];
  ```
- We can store any data type in an array, even other arrays and objects e.g.
  ```js
  const myArray = [
    "a string",
    5,
    false,
    [1, 2, 3, "something"],
    null,
    { name: "Bill", age: 294 },
  ];
  ```
- Each position in an array is called an index, but the index starts at 0 so the first element is at index 0, the second element is at index 1 and so on
- We can access an element in an array by using the index which we write in square brackets after the name of the array e.g.`console.log(myArray[0])` to get the first element in myArray
- Arrays have methods and properties that we can use, we replace the word array with the name of the array we want to access these methods/properties of:
  - `array.length` returns the length of the array
  - `array.splice()` can add and remove elements anywhere in the array when we give it arguments of the index to start counting from, the number of elements to delete, and the new element(s) we want to add
  - `array.pop()` removes the last element in the array and returns the value of that element
  - `array.push()` adds the argument to the end of the array
  - `array.shift()` removes the first element in the array and returns the value of that element
  - `array.unshit()` adds the argument to the beginning of the array at index 0
  - `array.reverse()` which reverses the order of elements in the array but doesn’t create a copy of it
</details>

<details><summary><h3>JS Loops</h3></summary>

- Loops are used for tasks we want to repeat without having to invoke a function each time
- There are several types of loops:
  - `while` - executes the task while the condition is true and stops when it is false
  - `do… while` - executes the task once then checks if the condition is true to start repeating the task like a while loop
  - `for` - executes the task a set number of times and performs an afterthought after each execution, this can be used for arrays by using the array.length property as
  - `for… of` - executes the task once for each element in an array and assigns each element to a variable that can be used in the loop
  - `forEach()` - an array method that calls a callback function once for each element in an array
- Loops are how we can iterate through the elements in an array to perform certain actions for each element
</details>

<details><summary><h3>JS Callback Functions</h3></summary>

- When we give a function as an argument for another function we call it a callback function
- Some methods we use require a callback function to be one of the arguments, an example is the `array.forEach()` method
- We don't have to use a previously declared function as a callback, we can also use an anonymous or arrow function that we write as the argument
- The syntax for declaring a function that takes another function as an argument could look like this:
  ```js
  function myFunction(callbackFunction) {
    callbackFunction();
  }
  ```
  - Remember: we don't need brackets when we write the function as an argument or a parameter, just the function name
- The syntax for calling a function that takes another function as an argument could look like this:
  ```js
  myFunction(anotherFunction);
  ```
  - This will run `myFunction` but also run the callback function `anotherFunction` in place of the parameter that we added when declaring the function
</details>

<details><summary><h3>JS Objects</h3></summary>

- Objects are a way of storing multiple pieces of data related to each other, different to an array which stores a list of data
- Objects are made up of key-value pairs which we can also call properties
- Objects are written in curly brackets with each key-value pair separated by a comma
- An example object representing a person:
  ```js
  const thisPerson = {
    name: "Bob",
    age: 29,
    favouriteColour: "red",
  };
  ```
- Objects can contain more than just strings and numbers, they can also contain arrays and other objects:
  ```js
  const complexObject = {
    string: "This is a string",
    numberOfProperties: 4,
    numberList: [1, 2, 3],
    secondObject: {
      anotherString: "This is a string inside an object inside another object",
      favNumber: 5,
    },
  };
  ```
- To access properties inside an object we use dot notation, meaning we write the name of the object followed by a dot then the name of the property that we are accessing:
  ```js
  console.log(user.userName);
  ```
- Several things we interact with in JavaScript are actually objects that we can access properties of using dot notation including the document, the console, and arrays
</details>

<details><summary><h3>JS Object Methods</h3></summary>

- Objects don't just have to store our standard data types, they can also store functions that we can invoke using dot notation
- A function within an object is called a method
- You will have already seen array methods such as `array.push(),` DOM methods such as `document.createElement(),` and console methods such as `console.log()`
- We can create our own object methods by creating a function within an object:
  ```js
  const personObject = {
    name: "Jerry",
    age: 40,
    occupation: "software dev",
    sayHello: function () {
      console.log("Hello!");
    },
  };
  ```
  - We can call the above method like any other method through dot notation:
    ```js
    personObject.sayHello(); //Output: Hello!
    ```
- We can refer to properties within the object using the keyword this:
  ```js
  const personObject = {
    name: "Jerry",
    age: 40,
    occupation: "software dev",
    sayHello: function () {
      console.log("Hello!");
    },
    greetPerson: function () {
      console.log(
        `Hello, ${this.name}! You are ${this.age} years old, and you are a ${this.occupation}!`
      );
    },
  };
  ```
</details>

<details><summary><h3>CSS Grid</h3></summary>

- Another tool we can use for the layout of our apps is grid
- Grid creates a layout of rows and columns which we can then use to size and position elements
- To create the grid we need to use the display property with a value of grid and create a template of rows and columns with a size and number:
  ```css
  .parentBox {
    display: grid;
    grid-template-columns: 20px 50vw 20px;
    grid-template-rows: 1fr 2fr;
  }
  ```
- We often use the `fr` unit for grid templates which refers to a fraction of the total remaining size
  - If we only use `fr` the rows/columns will divide the whole size in that ratio, so the rows in the above example will be 1/3 and 2/3 of the parent element's height respectively
  - If we use `fr` alongside other units the rows/columns will split only the remaining size after creating rows/columns with the fixed sizes
- To assign a child element to a position on the grid created in its parent we use the `grid-column` and `grid-row` properties:
  - We can use `grid-column-start`, `grid-column-end` and `grid-row-start`, `grid-row-end` to set the start and end row and column separately:
    ```css
    .childElement {
      grid-column-start: 0;
      grid-column-end: 4;
      grid-row-start: 0;
      grid-row-end: 3;
    }
    ```
  - To keep it more concise we can use `grid-column` and `grid-row` and include the start and end values in one property separated by a slash:
    ```css
    .childElement {
      grid-column: 0 / 4;
      grid-row: 0 / 3;
    }
    ```
- There are a lot of different techniques we can use to create a grid and position elements on it, here are a few alternatives to what is covered above:
  - The `grid-template` property lets us combine the `grid-template-columns` and `grid-template-rows` properties into one big one with the rows first followed by the columns:
  ```css
  .parentBox {
    display: grid;
    grid-template: 1fr 2fr / 20px 50vw 20px;
  }
  ```
  - The `repeat` function can be used when creating the grid template to create multiple rows/columns with the same dimensions, and can be used alongside regularly defined rows/columns:
  ```css
  .parentBox {
    display: grid;
    grid-template-columns: repeat(4, 25%);
    grid-template-rows: 20px repeat(2, 50px) 20px;
  }
  ```
  - The `grid-area` property lets us position elements with the rows and columns combined into one property in the order row-start, column-start, row-end, column-end :
  ```css
  .childElement {
    grid-area: 0 / 0 / 3 / 4;
  }
  ```
  - The `span` keyword can be used to tell the element to span a certain number of rows/columns instead of providing a fixed row/column to end on:
  ```css
  .childElement {
    grid-column: 0 / span 3;
    grid-row: 0 / span 2;
  }
  ```
  - The `grid-template-areas` property lets us define rows and columns more visually by creating a grid that uses named areas that we can assign our elements to:
  ```css
  .parentBox {
    grid-template-areas:
      "header header ."
      "aside main main"
      "footer footer footer";
  }
  ```
  - We can use this alongisde `grid-area` to position our elements in these grid areas that we've defined:
  ```css
  .childElement {
    grid-area: header;
  }
  ```
</details>

<details><summary><h3>CSS Media Queries</h3></summary>

- Media queries allow us to apply styling based on conditions like screen size, dark mode/light mode, and orientation
- By using media queries we can make our apps more responsive and accessible across different devices
- Media queries are written in the same css file as the rest of our styling, but usually go at the bottom as css is read top-to-bottom and we want to override the styling we have written
- We write the styling we want as normal but the styling written within a media query will override this when the conditions of the media query are met
- Here is an example of a media query that will take effect on screens above 600px wide:
  ```css
  @media (min-width: 600px) {
    .flexContainer {
      flex-direction: column;
    }
  }
  ```
- The media query doesn't need to contain every css property we want to be applied, those come from the regular css we write in the rest of the file, but just the ones we want to override when the conditions are met
- We don't usually want to resize elements in media queries and mostly use them for changing layout as we can use responsive units like `vw` (view width) and `vh` (view height) to have elements take up a certain proportion of the screen at any screen size
- Most of the time we want to design for mobile first and use media queries to apply styling for larger screens, as doing it the other way around is often more difficult
</details>

<details><summary><h3>HTML Image, Audio, and Video Elements</h3></summary>

- You will already be familiar with the `<img/>` tag but may not have used the `<audio/>` and `<video/>` tags
- The `<img/>` tag needs a src attribute that lets us define the source of the image whether it is stored locally or hosted online
- The `<audio/>` tag similarly needs a src attribute to get the audio from, but also needs the controls attribute to add the browser's default audio controls so the audio doesn't just autoplay
- The `<video/>` tag also needs a src attribute to get the video from, and like `<audio/>` it also requires the controls attribute so we can't force users to watch a video with no way to pause it
</details>

<details><summary><h3>JS Interaction with Audio and Video Elements</h3></summary>

- The `<audio/>` and `<video/>` tags don't actually _need_ the controls attribute all of the time, as we can actually build our own controls using JavaScript
- At minimum our controls should include play, pause, and volume controls
- We can use events such as play, pause, load, and timeupdate on `<audio/>` and `<video/>` element just as we would use any other event to run a function when the users plays, pauses, etc.
- `<audio/>` and `<video/>` elements have methods we can use such as `.play()` and `.pause()` that do exactly what we would expect, as well as properties such as `.volume` and `.muted` that we can also change the value of
  - This means we can create buttons like pause and play then add events that run these methods, allowing us to create our own controls for our media elements
</details>

<details><summary><h3>Accessibility with ARIA and WCAG</h3></summary>

- ARIA (Accessible Rich Internet Applications) and WCAG (Web Content Accessibility Guidelines) are common acronyms that you'll see in relation to building accessible apps
- ARIA is a set of attributes that we can add to HTML elements to improve accessibility to add more functionality to screenreaders, often with the aria-label attribute that lets us describe specific features of an app that don't have an equivalent HTML tag
- Another common ARIA attribute is aria-live which we can use to have screenreaders read content when it changes, as in the case of an image gallery where the image displayed would change perhaps
- WCAG is an evolving set of guidelines explaining how to make web content more accessible to people with disabilities, the guidelines can be found here - https://www.w3.org/WAI/standards-guidelines/
- The A11Y checklist is a checklist of items we can check to see if our apps are compliant with WCAG in different aspects - https://www.a11yproject.com/checklist/
- To test how accessible our apps are we can enable voiceover (CTRL+WIN+ENTER on Windows or CMD+F5 on Mac) and explore our different pages and features to see how well they're described by the voiceover
- A quick metric we can use to test the accessibility of our apps is Google's lighthouse report which we can run from the lighthouse tab in the Chrome dev tools to see scores for performance, accessibility, best practices, and SEO
  - The lighthouse report returns some really useful data about what we can improve to increase the score in each category, so we can act on this to achieve higher scores and better accessibility practices
</details>

<details><summary><h3>Image Optimisation with srcset</h3></summary>

- Images are larger and take longer to download than text so they can have a big effect on the performance of our apps
- It makes sense to use better optimised images to ensure that users on older and/or slower devices can still achieve the desired experience on the app
- We should usually use the smallest image possible for our use case to increase performance, so thumbnails shouldn't be a large image file with the height and width adjusted through CSS as this is still a large file
- The performance of any website can be tested using the browser's dev tools with the performance tab (in Chrome) to see statistics like the largest contentful paint (how long it takes to load the largest image, video, or block of text on initial page load)
- Each of the common image formats has its own strengths and weaknesses and this extends to how optimised they are:
  - JPEG - Lossy, good for photographs. Adjust quality to reduce file size.
  - PNG - Lossless, suitable for images with fewer colors, supports transparency.
  - GIF - Limited to 256 colors, ideal for animations.
  - WebP - Modern format, offers better compression than JPEG
- We can use the `srcset` attribute to use different files with differently sized images at different screen sizes, ensuring that the user has the appropriately sized image for their screen size:
  ```html
  <img
    src="https://picsum.photos/2000/1000"
    srcset="
      https://picsum.photos/500/250    500w,
      https://picsum.photos/1000/500  1000w,
      https://picsum.photos/2000/1000 2000w
    "
  />
  ```
  - The url is used as the `src` and the `width` value (e.g. 500w) is the screen width at which that specific `src` will be used, with each pair of `src` and `width` separated by a comma
  - The appropriate src is selected from srcset on initial page load so it won't work with resizing the window in dev tools like media queries do
</details>
