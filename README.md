# SASS from scratch #

## Experimenting with SCSS ##
1. Clone this repository to your development computer.
2. Run `npm install`. This will read the value for the `"devDependencies"` section in the `package.json` file, and will install all the NodeJS modules needed for the project (and all _their_ dependencies) in a directory called `node_modules`.
   
   In particular, it will install a module called `live-server` and another called `sass`.
   * The `live-server` module will be used to serve the `index.html` page inside the `src/` directory.
   * The `sass` module will watch the files inside the `src/scss` folder, and each time you save a change to one of the files, it will regenerate a `.css` file inside the `src/styles/` directory.

   Whenever the contents of the `src/styles/` directory changes, `live-server` will refresh the display in your browser, so that you can see the result of your changes to the `scss` files, in real time.

> Note that the `.gitignore` file contains an entry for `node_modules/`. This means that when you commit your work and push it to your repository on GitHub.com, the (huge) `node_modules/` directory will not be uploaded. Your collaborators will need to run `npm install`, in order to recreate the `node_modules/` directory on their own development computers.
3. In a Terminal window, `cd` into the directory that contains this README.md file, if you are not there already.
4. Run the command `npm start`.
   This will look in the `"scripts"` section of the `package.json` file in order to know what to do. The `start` script will launch two processes:  
   * `live-server src`: to display the file at `src/index.html` in your browser, and update it in real time
   * `sass`: to update the files in your `src/styles/` directory, each time you save changes to a file in the `src/scss` directory.
5. Make changes to the `index.html` file in the `src/` directory, and also to the file at `src/scss/main.scss`.
6. Observe how your web page updates in your browser in real time.

## Inspiration on what to practise

Visit the [SASS Basics](https://sass-lang.com/guide) page. Read the Preprocessing section, and check that you understand how the `"watch": "sass --watch src/scss:src/styles"` script in the `package.json` file works.

Practise using the different features of SASS:
* Variables
* Nesting
* Partials
* Modules
* Mixins
* The @extend directive
* Operators

---

## Bonus activity: Deploying to GitHub
When you are satisfied with your work, you can deploy your files to GitHub, where anyone in the world can see your work.

In a Terminal window, run `npm run deploy`.

This command will automatically:
* Create a new branch called `gh-pages` (GitHub Pages)
* Push this branch to your GitHub repository
* Tell GitHub to create a new web site for you. 

Visit the root of you repository on GitHub, add `settings/pages/` to the URL, then visit the modified URL. (Alternatively, you can click on the `Settings` item at the right end of the menu bar, and then choose the `Pages` entry in the `Code and automation` section of the menu that appears on the left of the `Settings` page. These actions will take you to the same place.)

You should see a link...

> Your site is live at [`https://<organization-name>.github.io/<your-repo-name>/`]() 

...plus a button that says `Visit Site`.

It takes GitHub a little while to deal with your deploy request. (Your request will be placed in a queue. There are thousands of people interacting with GitHub at any given time). You might at first see a `404 Not Found` error, but wait few minutes then refresh the page.

### The `deploy` script

How did all this happen?

You'll find these entries in the `"scripts"` section of the `package.json` file.

```json
"clean": "rm -rf dist",
"clean:styles": "rm -rf src/styles",
"build:styles": "sass src/scss:src/styles",
"copy": "mkdir dist && rsync -av --exclude=\"/scss\" src/ dist",

"build": "run-s clean clean:styles build:styles copy",
"publish": "gh-pages -d dist",

"deploy": "run-s build publish"
```

Read these scripts and review the `bash` commands that you have already seen (`rm`, `mkdir`). Can you work out for yourself how the `deploy` script creates a cascade of actions?

To simplify slightly, these instructions are carried out in order:

1. `rm -rf dist`: If there is a `dist/` (distribution) directory, it is `r`e`m`oved `-r`ecursively and `f`orcibly. That is, the `r` option  tells the `rm` command to "`r`e`m`ove all the contents of the directory" and the `f` option tells it not to continuously ask you if you really want to `r`e`m`ove each of the files that will be deleted.
2. `rm -rf src/styles`: Does the same thing for the `styles/` directory
3. `sass src/scss:src/styles`: Tells the `sass` module to recreate the `styles/` directory that was just deleted
4. `mkdir dist`: Recreates the `dist/` directory that was deleted in step 1
5. `rsync -av --exclude=\"/scss\" src/ dist`: Uses the [`rsynch`](https://linuxize.com/post/how-to-use-rsync-for-local-and-remote-data-transfer-and-synchronization/) program to copy everything from the `src/` directory into the `dist/` directory, except the `scss` directory. In other words, it rebuilds the contents of the `dist/` folder.
6. `gh-pages -d dist`: Tells the `gh-pages` module to:
   * Create a branch called `gh-pages` which contains the files now in the `dist/` directory
   * Push this branch to your GitHub repository
   * Serve these files at a url with the format [`https://<organization-name>.github.io/<your-repo-name>/`](), as described earlier. 

> **NOTE**: the `run-s` command uses the [`npm-run-all`](https://www.npmjs.com/package/npm-run-all) module to `run` each of a set of commands `s`equentially (one after the other).