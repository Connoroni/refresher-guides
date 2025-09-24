# Week 5 Refresher Guide 

### Git Branching and Pull Requests
- This will be our first time collaborating in GitHub so there are a few useful features to cover
- In a GitHub repo, navigate to Settings> Collaborators then click 'Add people'
  - Send invites to your teammates via their name, email, or username
  - Find any pending invites in your emails or in the notifications on GitHub (at the top right of the screen, just next to the profile picture)
  - Now everyone can get the SSH link and clone the repo locally with ```git clone```
- One of the most useful Git features is branching, allowing one or more users to work on different features at the same time on different branches then merge them into the 'main' branch
  - To create a branch locally run ```git checkout -b branchName``` (this would create a branch called 'branchName')
    - ```git checkout branchName``` moves us to a branch with that name, but adding ```-b``` creates a new branch and moves us to it
  - On a branch you can add and commit as normal, but when you push from a branch we'll have to use ```git push origin branchName``` or ```git push origin HEAD```
  - To merge changes from a branch into main we need to make a pull request on GitHub 
    - A green 'New pull request' button should appear at the top of the code page in the GitHub repo, or we can go to the Pull Requests tab and click 'New pull request' there
    - Now we can create the pull request and merge it into main
  - It's common practice to delete a branch after it has been merged into main
  - With the branch merged, we can get the newest version of the code with ```git checkout main``` to put us on the main branch, followed by ```git pull``` to pull the latest version of the code from GitHub to our machine

### Multiple Pages in Vite
- As our apps get bigger and more complex we may want to start using multiple pages
- We can do this by creating a new directory and creating another ```index.html``` inside it
  - For example we can make an ```about``` folder inside our client and add an ```index.html``` in that folder to have a page that we can navigate to on ```/about``` e.g. 'localhost:5173/about'
- To ensure that this page gets built by Vite when we run ```vite build``` or more likely when we deploy to Render, we'll need to create a ```vite.config.js``` file in the client directory and add the following code
```js
import { resolve } from "path";
import { defineConfig } from "vite";

export default defineConfig({
  build: {
    rollupOptions: {
      input: {
        main: resolve(__dirname, "index.html"),
        about: resolve(__dirname, "about/index.html"),
      },
    },
  },
});
```

