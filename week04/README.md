# Week 4 Refresher Guide

### Vite
- Until now we've been using VS Code's Live Server extension to serve our code, but there are more advanced tools we can use through the command line that produce better results when we deploy the site
- Vite is a build tool that prioritises speed (Vite is French for 'fast') and optimisation
- To create our client using Vite we run the command ```npm create vite@latest``` and we'll be given a series of options here on the app we want to create
  - For project name: choose whatever you want to name the project
    - If we've already made the directory that we want our app to be in, then instead of giving it a name we can use `.` as the project name, meaning the app will be created in the current directory
  - For framework: just choose Vanilla
  - For variant: choose JavaScript
- Remember to ```cd``` into the project we've just created before doing anything else!
- Run ```npm install``` inside the new directory to install all the packages that come packaged with the Vite template
- We should now have a template Vite app with HTML, CSS, and JS files
- We have a new command to replace clicking Go Live, ```npm run dev``` which runs our dev server on localhost:5173
  - While the dev server is running, we won't be able to send any commands to the terminal, so we should either kill the script with ```CTRL+C``` or create a new terminal to have two running at once
- The other important command is ```npm run build``` which builds our app to prepare it for deployment with a few steps
  - Minification - All our code is minified to condense it to use as few lines and characters as possible in order to improve load speed and performance
  - Bundling - All the project files are bundled together into jsut a few files which reduces the number of HTTP requests needed and improves performance
  - Optimisation - Assets such as images and other static resources are optimised to be as efficient as possible
  - Code splitting - Our code is split into smaller chunks to be loaded on demand which improves performance by only loading code when it's necessary

### Building an API Server with Node and Express
- The client can only handle certain tasks, so at a certain point we'll have to create a server to handle tasks that involve manipulating data
  - That point is now!
- Our server is divided into different routes that handle different tasks, and we create endpoints to access each route
- The tasks our server will be handling will be HTTP requests such as GET and POST
- To set up a server we want to create a server directory separate to our client directory then run 
  ```
  npm init -y
  npm install express cors
  ```
- There's some boilerplate that we'll always want at the top of our server
  ```js
  // import express and cors from our node_modules
  import express from "express";
  import cors from "cors";
  
  // instantiate our express app
  const app = express();
  
  // tell express to expect information in the body of the request
  app.use(express.json());
  
  // tell express to use cors (Cross-Origin Resource Sharing) so our client can send data to the server
  app.use(cors();

  // start our server
  app.listen(8080, function () {
    console.log("App is running on PORT 8080");
  });
  ```
  - This is creating a server, telling it that the data we'll be sending will be JSON, and running it on port 8080
    - 8080 is a convention because it's an unreserved port, and possibly because programmers are nerds and it sounds like AT-AT
- We need to create an endpoint for each task we want our server to be able to perform, with the most basic one being GET
  ```js
  app.get("/", (request, response) => {
    response.json("This is the root route!")
  }
  ```
  - The string that is the first argument is the route of this endpoint, so by having "/" we are using the root route, in this case ```http://localhost:8080/```
    - We can create other routes with other get requests that give different responses
      ```js
      app.get("/messages", (request, response) => {
        response.json([{
          id: 1,
          message: "Hello",
          sender: "User"
        },
        {
          id: 2,
          message: "Goodbye",
          sender: "Bob"
        }]),
      }
      ```
      - This will create a new /messages route that will show an array of messages
- Run ```node server``` (assuming the server is in a server.js file) or create a ```dev``` script to run ```node server``` for you, and you should see the console log we set in the ```app.listen```
  - Now go to localhost:8080 and you should see the response from the root route, or go to the messages route and see the response there
- We can also create POST routes that handle POST requests to the API, usually coming from user input
  ```js
  app.post("/messages", (request, response) => {
    console.log("Message: ", request.body);
    response.json("Message recieved"); 
  }
  ```
- We can't test POST endpoints just by going to the URL like we can with GET endpoints because we use POST to create data not read it
- We'll need to use a service like Postman, Thunder Client, or HTTPie to test our POST endpoints instead
  - Give it the URL of the POST route and send a POST request in a JSON format, then see the response
- You'll also see endpoints written with ```(req, res)``` instead of ```(request, response)``` because devs are lazy


### Environment Variables
- Now that we're working with APIs and databases, we'll have sensitive information that we want to use without revealing so we can use environment variables
- Environment variables are useful for handling information we want to keep secret or placeholder information that we want to store in one place to change universally (e.g. localhost and deployed URLs)
- The package we'll use for handling environment variables is called ```dotenv``` and we can install and import it like any other package
- Create a ```.env``` file and a ```.gitignore``` file in the same directory as your ```server.js``` file
  - `.env` is the full file name, not just the extension
- In the .env file we define environment variables similarly to how we assign values to variables in JavaScript, using a name and a value
  ```
  API_KEY = "an_example_api_key_1234"
  IMPORTANT_URL = "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
  MY_COLOUR = "purple"
  ```
- In the `.gitignore` file just add `.env` to make sure our environment variables don't get pushed to GitHub and leak our secrets
- We make environment variables available in our JavaScript with ```dotenv.config()``` near the top of the file, somewhere in our existing server boilerplate which makes the environment variables available through the ```process.env``` object
  ```js
  import express from "express";
  import cors from "cors";
  import dotenv from "dotenv";

  const app = express();
  app.use(express.json());
  app.use(cors());
  dotenv.config();
  
  app.listen...
  ```
- Now we can get a value out of ```process.env``` like any other object using dot notation
  ```js
  console.log("My favourite colour is ", process.env.MY_COLOUR
  ```
  Or for a more relevant example
  ```js
  app.get("/cool-data", (req, res) => {
    const API = `https://api.my-api.com/data?user_key=${process.env.API_KEY}`;
    const response = await fetch(API);
    const data = await response.json();
  
    res.json(data);
  };
  ```
- Since our environment variables are ignored by GitHub due to our `.gitignore` file, they won't be picked up by our deployed site so we'll have to provide them while setting up the deployment

### Deploying a Client and Server on Render
- To deploy the server to Render go to the 'Add new' dropdown and select 'Web Service'
  - Choose the GitHub repo containing both the client and server
  - Set 'Root directory' to be server, as we only want to deploy the server code here
  - Set 'Build command' to be ```npm install``` so all of our packages are installed
  - Set 'Start command' to be ```node server.js``` to run the server code just like we do locally
  - Add your environment variables at the bottom of the page
    - You can copy and paste multiple environment variables from your .env file into here at once and they will automatically fill in multiple rows
  - Finally just click to deploy the server and navigate to the URL that you're given to access it
- To deploy the client to Render go to the 'Add new' dropdown and select 'Static Site'
  - Choose the GitHub repo containing both the client and server
  - Set 'Root directory' to be client, as we only want to deploy the client code here
  - Set 'Build command' to be ```npm install; npm run build``` so all of our packages are installed and vite is used to build our app
  - Set 'Publish directory' to be dist as this is the directory that the minified code is stored in after building
  - Finally just click to deploy the client and navigate to the URL that you're given to access it
- Make sure to update the client code to point to the URL of the deployed server, not localhost
- Note that Render servers shut down after 15 minutes of inactivity, so the next request after this will take longer as the server is waking up
  - A simple way to check whether the server is active is to go to the deployed server URL and check if you see the root route or the Render loading page

### User Inputs and POST
- We can combine HTML forms with POST endpoints to allow for more meaningful user inputs, using fetch to send the data the user inputs to the server
- We set up a form like normal in HTML
  ```html
  <form id="nameForm">
    <label for="name">Enter your name</label>
    <input type="text" name="name" id="nameInput" />
    <button type="submit">Submit</button>
  </form>
  ```
- Then in our client-side JavaScript we set up a "submit" event listener but include a fetch with ```method: "POST"```
  ```js
  const nameForm = document.getElementById("nameForm");
  
  function submitHandler(e) {
    e.preventDefault();
    const formData = new FormData(nameForm);
    const name = formData.get("name");
  
    fetch("http://localhost:8080/names", {
      method: "POST",
      headers: {
        "Content-Type": "application/json", 
      },
      body: JSON.stringify({ name }),
    }
  };
  
  nameForm.addEventListener("submit", submitHandler);
  ```
- Now in our server.js we can create a POST endpoint to handle the user-submitted data
  ```js
  app.post("/names", express.json(), (req, res) => {
    console.log("Hello ", req.body);
    res.json({ status: "New name received" });
  }
  ```

### Databases and SQL
- Databases are the third part of the WRRC (Web Request-Response Cycle) and allow us to store data that can be accessed by the server and sent to the client
- Think of databases like spreadsheets, each made up of tables with rows and columns
- Each table stores data about a certain type of thing (e.g. users, posts, books, films) with a column for each property of an object (e.g. name, id, age, genre) and a row for each unique entry (e.g. a single user, post, book, or film)
- The type of database we're interested is a relational database, which is one that represents the relationships between different pieces of data across multiple tables (e.g. all posts by a certain user, all books with a certain genre)
- SQL (Structured Query Language) is the language we use to query and manipulate data in a database and the variant we'll be using is PostgreSQL aka Postgres
- Different databases use different versions of SQL such as SQLite and MySQL, so make sure to include Postgres when you're searching for resources
- The first step in setting up a database is to create a project on [Supabase](https://supabase.com/) through the dashboard
- With a project created we can now use the SQL editor in Supabase to write the SQL that we will use to create the tables, insert data, etc.
- The first queries we want to write will be to create each table using the ```CREATE TABLE``` query and giving it the name of the table and the names and constraints of each column
  ```
  CREATE TABLE table_name (
    column_name BOOLEAN
  )
  ```
  - This will create a table called table_name with a column called column_name that stores booleans only
    - The command in all caps after the table name is called a constraint and determines the type of data that can be stored in this column 
- A more common CREATE TABLE query would look something like this
  ```
  CREATE TABLE users (
    id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    username VARCHAR(255),
    bio TEXT
  )
  ```
  - The most important column here is id as it is the primary key meaning it is the main identifier for each entry, and adding ```GENERATED ALWAYS AS IDENTITY``` means a unique one is generated for each new record in the table
  - The other constraints here are some of the data types we'll see and use most often:
    - INT is a number (specifically a 4 byte number, anything bigger needs BIGINT)
    - VARCHAR is for a string up to a certain number of characters, often 255 but it can be higher or lower
    - TEXT is for a string of any length so is best used for strings that could be longer than our VARCHAR limit including URLs or larger blocks of text
- Another common query is ```INSERT``` which is used, unsurprisingly, for inserting data into an existing table
  ```
  INSERT INTO users (username, bio) VALUES (
    'CoolUsername123',
    'I am a user who wants to use this site. Oh boy!'
  )
  ```
  - Here we need to specify the name of the table, the name of the columns, and the values to insert into each of those columns to create this entry
  - Notice that we don't insert into the id column as the id is generated for each new entry
- If we want to view the data in a table we use the ```SELECT``` query
  ```
  SELECT * FROM users
  ```
  - This will simply select all columns from the users table and display all the data as we have used the ```*``` to select everything, similar to its use in CSS
  - If we want to select only certain columns we can write the column names instead
    ```
    SELECT username, bio FROM users
    ```
  - If we want to select only certain records we can add a ```WHERE``` condition
    ```
    SELECT * FROM users WHERE id = '1'
    ```

### Connecting a Database to Our Apps
- We use a package called pg (it stands for Postgres) to connect a database to our server, and then we just fetch the data from the server to the client like we've done with APIs
- Install the ```pg``` and ```dotenv``` packages in the server directory like any others with ```npm init -y``` and ```npm i pg dotenv```
- We want to create a db object using the ```pg.Pool``` method and use it to send queries to our database 
  ```js
  const db = new pg.Pool({
    connectionString: process.env.DB_CONN,
  });
  ```
  - ```db``` is an arbitrary name but a useful convention to stick to so we don't have to check where our database connection is stored
  - To get the DB_CONN environment variable, go to the Supabase project and click 'Connect' at the top of the screen, then copy the link under 'Transaction pooler'
    - This will include [YOUR-PASSWORD] so we'll need to replace this with the actual password for the database
    - Generate a new password by navigating to Database> Settings> Database password (or Project Settings> Database> Database password) and clicking 'Reset database password' then 'Generate a password'
      - Remember to click the green 'Reset password' button after generating and copying a new password, this is a very easy step to forget!
    - Store this in your .env file and you can access it with process.env like any other environment variable
- Most of the time, the queries we want will come from user input or loading a page but we can create a ```seed.js``` file in the server to run queries through node.js
  - This is useful for being able to recreate tables from scratch if they get deleted or we need to recreate the database
  - In the seed file we can query the database with the ```db.query``` method where we can write queries just as we would in Supabase's SQL editor
    ```js
    db.query(`
      CREATE TABLE messages (
        id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
        message_name VARCHAR(255),
        message_content TEXT
      )
    `);
    ```
  - Run the file with ```node seed``` and this table will be created in your database 
- We can use INSERT queries here but we often want the data inserted to vary, so we can use placeholders in the query with ```$``` followed by an array containing values to be substituted for the placeholder
  ```js
  db.query(
    `INSERT INTO messages (message_name, message_content) VALUES ($1, $2)`,
    ['hello there', 'general kenobi!']
  )
  ```
  - This will insert the message name and content from the array into the workshop instead of '$1' and '$2'
  - You may think we could do this with template literals but this would cause a problem
    ```js
    db.query(
      `INSERT INTO users (name) VALUES (${req.body.username})`
    )
    ```
    - The above code would let malicous users enter values that would be interpretted as SQL queries e.g. entering a name of ```Robert'); DROP TABLE users;--``` would cause problems as a query could become
      ```js
      db.query(
      `INSERT INTO users (name) VALUES ('Robert'); DROP TABLE users;--)`
      )
      ```
      - This would insert the name 'Robert' but then end the query and begin a DROP TABLE query that would delete the users table, which would be anywhere from quite bad to completely catastrophic depending on the database
- Most of the time we'll be querying the database not from a ```seed.js``` file but from a ```server.js``` file where we'll set up endpoints to send our queries
  ```js
  app.get("/users", async (req, res) => {
    const users = await db.query(`SELECT * FROM users`);
    res.json(users.rows);
  });
  ```
  - A query will return an object containing data about the query, but what we usually want is the ```rows``` property in this object that contains an array of the rows returned by a SELECT query
- We can also create a POST endpoint to insert data into the database from the server
  ```js
  app.post("/messages", async (req, res) => {
    const data = req.body.submittedData;
    const query = db.query(
      `INSERT INTO messages (message_name, message_content) VALUES (
        $1, $2
      )`,
      [data.messageName, data.messageContent]
    );
    res.json(query.rows);
  }
  ```
  - Now we can connect this to the client with the fetch API so we can send data to the server to be inserted into the database, in this example using data submitted from a form
    ```js
    function submitHandler(e) {
      e.preventDefault();
      const formData = new formData(messageForm);
      const data = Object.fromEntries(formData);
      fetch("http://localhost:8080/messages", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ data }),
      });
    }
    ```

### Query Strings on API GET Routes
- Query strings are the part of a URL that comes after a `?` and are made up of key-value pairs like a JavaScript object
- When querying a pre-made API we can use query strings to be more specific about what data we want to filter down to
- In our client we'll create a function to fetch some data from an API like normal, but add a query string on the end to filter the data
  ```js
  async function getToDos() {
    const response = await fetch(
      "https://jsonplaceholder.typicode.com/todos?completed=false" // Notice what we've added?
    );
    const data = await response.json();
    console.log(data);
    return data;
  }
  ```
  - This will get the To-Do list from the JSON Placeholder API but filter it down to only those where the ```completed``` property is ```false```
- We can chain multiple query strings together using `&` to specify multiple key-value pairs
  ```js
  async function getToDos() {
    const response = await fetch(
      "https://jsonplaceholder.typicode.com/todos?completed=false&userId=4"
    );
    const data = await response.json();
    console.log(data);
    return data;
  }
  ```
  - This will filter the data to only the items where the `completed` property is `false` and the `userId` property is `4`
- We could allow users to choose how to filter the data by using a form input here and using fetch within a submit event handler
  ```js
  async function submitHandler(e) {
    e.preventDefault();
    const formData = new formData(userInput);
    const data = Object.fromEntries(formData);
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos?completed=false&userId=${data.userId}`
    );
    const data = await response.json();
    console.log(data);
    return data;
  }
  ```
