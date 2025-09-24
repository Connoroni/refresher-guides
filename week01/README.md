# Week 1 Refresher Guide

Congratulations, you’ve finished the first week! You’ve learned so much this week and it’s probably gone so quickly. Here's a refresher of everything we've covered so you can refer back to it.

### How the Internet Works

- The WRRC (Web Request Response Cycle) - how the client and server communicate by sending requests and recieving responses
- The DNS - converts a website’s domain name to an IP address
- Parts of a URL - protocol://domain.name/path?query=string

### The Terminal

- Navigate between directories using `cd`
  - We can go up by one directory using `cd ..`
  - We can move multiple directories at once by using a `/` e.g. `cd bootcamp/week01/html-tags`
- Create new directories using `mkdir`
- Show the contents of the current directory using `ls`
- Show the path of the current directory using `pwd`
- Create new files using `touch`, but remember the file extension e.g. `touch index.html`
- Open the current directory in VS Code using `code .`
- We always use a command followed by the thing we're running the command on (called an operand)
  - For example we can't just do `mkdir` we have to do `mkdir directoryname`, and we can't just do `touch` without specifying the file name `touch index.html`
- We can autocomplete in the terminal using the `Tab` key if we start typing the name of a file/directory

### VS Code

- Open directories in VS Code using code . in the terminal
- Create files in VS Code
- Start with `index.html`
  - `index.html`

### HTML Tags

- HTML elements are created with opening and closing tags with content between them
- Some elements are self closing and have no content e.g. `<img/> or `<br/>`
- Tags are the bits enclosed in the `<` `>` but elements are the whole thing with the tags and the content
  - You may hear these terms used interchangably if we talk about "creating a `<p>` tag" etc.

### HTML Attributes

- We can modify HTML elements by adding attributes
- Attributes go inside the opening tag e.g. `<img src="image-source.jpg">`
- Examples of attributes are src, alt, href, class, and id

### HTML Structure

- We should use semantic HTML tags that match how the element is used on the page
- The `<head>` tag holds metadata such as the page title and linked CSS and JS files
- Divide our `<body>` tag into `<header>`, `<main>`, and `<footer>`
- Use `<h1>` for the main title (only one on the page), `<h2>` for smaller headings, `<p>` for paragraphs
- Use `<div>` and `<section>` to dive the page up and group elements together
  - Generally `section` should be used for meaningful sections on the page while `div` is used for elements we want to group for styling or for other reasons

### CSS Properties

- Link our CSS file to our index.html using `<link rel="stylesheet" href="fileName.css" />`
- Emmet abbreviations in VS Code will autofill this if we type `link:css`
- Select the element we want to style in CSS and write the styles between {curly brackets}
- Styles are written with the property name followed by the value e.g.
  ```css
  element {
    property: value
  }
  ```
- Some common properties are `background-color`, `color` (for text color), `height`, `width`, `display`, `margin`, and `padding`
  - Remember: We have to spell 'color' the American way without a 'u'!

### CSS Selectors

- Elements can be selected using more than just the tag name
- We often use `.class` to target specific elements or groups of elements, using a `class` attribute that we've given them in HTML
- We can use `#id` to select elements but often prefer to use class because it’s reusable and keeps `id` exclusive to JavaScript
- There are more advanced selectors we can use based on an element’s relation to other parent, child, and sibling elements
  - Some selectors include `:first-child`, `:nth-child(A), A+B`, and `[attribute="value"]`
- We can use the universal selector `*` to select all elements
  - We may want to do this at the beginning of a CSS file to override browser default styles

### Dev Tools

- We can inspect a webpage (right-click and inspect) to open our dev tools and view the elements on the page
- The elements tab shows the full HTML layout of the page and allows us to make temporary changes
- The styles tab shows the CSS styles applied to an element, some from specific styling written by the dev and some from the browser default (user agent stylesheet)
- We can edit CSS styles in the styles tab manually and use the tickbox to toggle each property on and off entirely

### The Box Model

- Every element exists in a rectangular box, even if it the element doesn’t look like a rectangle
- The box doesn’t just include the size of the content in the element it also includes the `padding`, `border`, and `margin` that surround the element
- Padding pushes the content inwards from the border of the element
- Border creates a line (which can be solid, dashed, dotted, etc.) around the outside of the element between the padding and the margin
- Margin creates a space around the border of the element to push away other elements, but it doesn’t carry the styling of the element e.g. background-color

### CSS Positioning

- We can add the `position` property to an element in CSS, with values of `static`, `absolute`, `relative`, `fixed`, and `sticky`
- We add the `top`, `bottom`, `left`, and `right` values to "push" the element away from those borders e.g. `top: 20px` will position the element 20px away from the top
- Static - the default position value, the element exists in normal document flow
- Absolute - the element is taken out of the normal flow of the document (it is sent to the Shadow Realm) and doesn’t respect the position of other elements that it overlaps with, but only does this within its first parent element that isn’t static
- Relative - the element is positioned relative to other elements and still exists in normal document flow, this is often used to create a parent for an absolute element
- Fixed - the element maintains a specific position on the screen regardless of where the user scrolls
- Sticky - similar to fixed position the element maintains a specific position on the screen but only when the user scrolls to it

### CSS Flexbox

- Flexbox is a modern and responsive way of laying out elements on a page, letting us arrange them in a row or column
- We give the `display: flex` property in CSS to the parent element when we want to use flex on its children
  - Remember: it’s `display: flex` not `display: flexbox`!
- The `flex-direction` property is used to arrange items either in a row or column, but if we don’t add this property it defaults to row
- The properties `justify-content` and `align-items` are used to arrange the elements in the main axis and cross axis respectively
  - The main axis is the axis that the elements are arranged along, and the cross axis is the other axis
  - Values for these properties include `space-around`, `space-between`, `end`, and `centre`
- The `flex-wrap` property determines how the elements will be positioned if they fill the flex container, should they spill out of the container (`flex-wrap: no-wrap;`) or be pushed onto a second row/column (`flex-wrap: wrap;`)?
  - Remember: if the flex elements are wrapped we need to use `align-content` instead of alig`n-items`!
- Even if we have only one element, flex is a great way to centre it within its parent element

### Git Essentials

- Git is a version control system that allows developers to manage their code with updates, branches, and collaboration
- Github is a website created by Microsoft that uses Git, we use it to store our work remotely
- Using Git we have a local and remote version of our repo so we can work on it locally but have a version remotely that we can access anywhere
- Creating a repo:
  - On Github navigate to repositories and click "new"
  - Add a repository name
  - Tick the box to add a README file
  - Click "Create repository"
  - In your new repository click the green "Code" dropdown and click "SSH"
  - Copy the SSH key that appears
  - In your terminal navigate to the folder you want to clone your repo into
  - Type the command `git clone` followed by the SSH key you copied
  - Hit enter and the repo will be cloned locally
  - `cd` into the repo and you’re good to go!
- Pushing changes:
  - In your terminal navigate to your git repo
  - Use the `git add .` command to prepare all your updated files to be pushed
  - Use the `git commit` command (`git commit -m "commit message goes here"`) to add a commit message to explain what you’ve changed
    - Commit messages should explain your changes e.g. `git commit -m "create header"`
  - Use the `git push` command to push all your changes to Github, updating your remote repository to add the code you’ve written locally
- To deploy the site to Github pages go to settings > pages > branch and change the branch from "none" to "main" which will deploy the main branch to Github pages

### The Console

- The console tab in dev tools tracks anything logged to the console through the console API
- It’s most often used for errors and for printing values as our code executes
- Very useful for debugging as we can keep track of values to see what the app is doing
- The most common JavaScript method we use to add to the console is `console.log()`
- We can also use the method `console.error()` to have the message appear as an error in the console instead of a standard message

### JS Variables

- Variables are a way of storing data in JavaScript, allowing us to refer to the variable name in our code instead of the value
- Values in a variable can be any data type whether that’s a primitive one like a string or number, or a complex one like an object or array
- We can declare variables with the keywords let (for variables where the value will change) or const (for variables that have a constant value)
- When we declare a variable we use `let variableName = value` or `const variableName = value` but when we change the value we drop the keyword so it's `variableName = value`
- Using let we can declare a variable with no initial value, just `let variableName`

### JS Primitive Data Types

- Data is information that we store in the computer's memory, but there are different types of data that get treated differently so we can do maths with numbers but not with words
- The primitive data types are: `number`, `string`, `boolean`, `undefined`, and `null`
- Number - self explanatory, it’s a number that we can do maths with
- String - anything within "speech marks", 'apostrophes', or \`backticks` is a string which is usually text
- Boolean - a binary true or false value, usually it’s the word true/false or other values that we call "truthy" and "falsy"
  - Most values are considered truthy but the most common falsy value is 0
- Undefined - a value hasn’t been assigned yet, usually seen if we try to access a variable that has no value
- Null - not to be confused with undefined, null is a value that is assigned manually to represent nothing
- An easy way to see different data type behaviours is trying to do maths with them, only numbers will add together as expected while everything else will concatenate

### The DOM (Document Object Model)

- The DOM is the data representation of the HTML document in the browser, representing it as a series of nodes and objects
- Nodes are the individual elements and attributes in the HTML document
- We can manipulate the DOM through JavaScript which allows us to select and alter existing elements and attributes or create new ones
- The main JavaScript methods we use to select existing elements are `document.querySelector()` and `document.getElementById()`
  - `document.getElementById()` selects elements by the id as you may have guessed
  - `document.querySelector()` can select elements similarly to how we do in CSS using class, attributes, etc.
  - It can be easier to get into the habit of always using 
- We usually select DOM elements by assigning the return value of these methods to a variable e.g. `const myImg = document.getElementById(“myImg”)`
- The selected element is an object containing many different properties and methods
  - Once assigned to a variable we can manipulate the properties of the element such as `textContent`, `className`, style the same way we would alter the properties of any object e.g. `myTitle.textContent = "This is a Title"`
  - We can also invoke methods of the element object such as `setAttribute()`, `addEventListener()`, and `appendChild()`
- We can create new elements as DOM nodes using the `document.createElement()` method, adding an argument to specify the tag we want to create e.g. `const newImg = document.createElement("img")`
- We can remove an element from the DOM by selecting it then using the `remove()` method e.g. `newImg.remove()`

### JS Conditionals

- Conditionals are how we tell a block of code to run only under certain conditions
- We use the keywords `if`, `else if`, and `else`
  - `if` - Check if the condition is true and execute the following code if it is
  - `else if` - Check if the condition is true after checking the above condition(s) and execute the following code if it is
  - `else` - Execute the following code only if none of the above conditions are true
- The syntax for each keyword is:
  ```js
  if (condition) {code to execute}
  ```
  ```js
  else if (condition) {code to execute}
  ```
  ```js
  else {code to execute}
  ```
  - A typical conditional could look something like this
    ```js
    if (username === "Jeff") {
      console.log("You are Jeff and you're allowed here")
    } else if (username === "Bob") {
      console.log("You are Bob and you're not allowed her")
    } else {
      console.log("You are ", username, " but I don't know you")
    }
    ```
- Like all JavaScript, conditionals are read top to bottom so the code will execute the first block of code where the condition is true even if there are multiple conditions that are true

### JS Functions

- Functions are self-contained blocks of code contained in {curly brackets} that execute a certain task
- Functions help break up our code into smaller pieces and can be reused whenever we want the task to happen
- The syntax for declaring a function:
  ```js
  function myFunction() {
    console.log("Something");
  }
  ```
- We need to declare a function using the syntax above then invoke it using the function name followed by a pair of parentheses ()
  ```js
  // Declare the function
  function myFunction() {
    console.log("Something");
  }
  
  // Then invoke the function
  myFunction();
  ```
  - We often say that we 'call' a function instead of 'invoke' it, this means the same thing but is one syllable shorter
- Functions can be given arguments which allow us to vary exactly what the function does depending on the data we give it as an argument e.g.
  ```js
  function myFunction(word) {
    console.log(word);
  }
  myFunction("hello"); //This will log hello
  ```
  - The parentheses in the function declaration contain the parameters, but the parentheses when we call the function contain the arguments
  - Arguments take the place of wherever we used the parameters when writing the function
- Not all functions need names as not all of them need to be called elsewhere, so we can create anonymous functions in similar syntax:
  ```js
  function() {
  console.log("This function has no name");
  }
  ```
- Taking anonymous functions even further we can create arrow functions which also have no name but don’t even need the function keyword:
  ```js
  () => {
    console.log("It’s an arrow function because we use an arrow(=>)")
  }
  ```

### JS Scope

- Not all variables we declare can be used anywhere in our code and it depends on the scope of the variables
- A variable declared outside of any function can be used anywhere, this is called global scope
- A variable declared inside a function can only be used inside that function, this is called function scope
- For larger apps using too much global scope can cause memory problems because the variable is never cleared from memory so it can be accessed at any time, while variables within function scope only exist while the function is being executed
- We can reuse variable names if they have function scope but we can never reuse the name of a globally scoped variable

### JS Events

- Events are how we allow users to interact with our JavaScript by adding event listeners that "listen" for a certain event
- The two parts of an event are the event listener and the event handler
  - Event listener - A property added to DOM elements with the `addEventListener()` method
  - Event handler - A function that executes when the event we’re listening for happens
- The `addEventListener()` DOM method takes two arguments, first is the event to listen for (e.g. "click") and second is the function to run when that event happens
  ```js
  myButton.addEventListener("click", buttonHandler);
  ```
- The most common event we listen for is "click" but there are many other options including "submit", "keydown", "scroll", and "load"
- Event handlers can be a function created elsewhere which we invoke by name but often it is an arrow function if we only want to use the function for this one event listener
