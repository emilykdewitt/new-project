# Everything To Start a New Project

# Webpack Installs

## npm installs
### Package.json
To install 3rd party libraries we will be using npm for the duration of class.  To get started you need to initialize your project as one that uses npm.  Run the following command:
```js
npm init -y
```

This command should create a file called package.json in the root of your project.  The file should look something like this:
```json
{
  "name": "new-repo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

Now that we have made the file we can go in and modify it using vs code.  Change the main, scripts, author, and license values in your package.json file to look like this:

```json
{
  "name": "new-repo",
  "version": "1.0.0",
  "description": "",
  "main": "src/javascripts/main.js",
  "scripts": {
    "start": "webpack-dev-server --mode development --open",
    "build": "webpack --mode production --module-bind js=babel-loader"
  },
  "keywords": [],
  "author": "Zoe Ames",
  "license": "MIT"
}
```

This file is where we will save references to the packages we are going to use in the project.  This allows future users to install what they need to get things running.  There are two types of ways we will save things - dev dependences and regular dependencies.

Dev dependences are for things that are required in order to develop the code.  In our case that is all things webpack.

Regular dependences are things that are required to be installed in order for the code to run.  In our case that will be things like Jquery and Boostrap.

### Installing Dev Dependencies
To install the dev dependences we need run the following line of code:
```sh
npm install @babel/core @babel/preset-env babel-loader css-loader eslint eslint-config-airbnb-base eslint-loader eslint-plugin-import file-loader html-loader html-webpack-plugin mini-css-extract-plugin node-sass sass-loader webpack webpack-cli webpack-dev-server --save-dev
```
After you do this you will see that you should now have a folder called node_modules with a BUNCH of stuff in it.

If you take a look at your package.json file you should see a section called devDependencies that includes a list of all the packages we just installed.  Our package.json file knew to put these things as dev dependencies because at the end of the line we have a `--save-dev`.

After doing the installs you should also see a file called package-lock.json.  This file keeps track of the EXACT version numbers that got installed.  It also keeps track of any dependences of your dependencies and their exact versions.

## .gitignore
Now that we are compiling our code and installing third party packages we need a way to keep our projects from pushing up code that does not belong to us.  To do this we create a file called .gitignore that goes at the root of our project.  This file tells git to NOT track anything inside it.  We will add the following files:
```sh
package-lock.json
node_modules/
dist/
build/
```

What are these things we are ignoring?

The `node_modules` folder is where all the third party libraries we are going to install live.  Since this is not our code we don't want to push it up to github.  People who decide to download the project and run it can install the stuff directly from our package.json file.

The `package-lock.json` file is used to track what specific version of each package you are using.  Unless you are working on a complex production level application it isn't really needed and tends to add a lot of extra info to PRs.

The `dist` and/or `build` folders are created by webpack when you create a production build of your code.  They are minified and ugified versions of the existing code - ie a smaller duplication.  We don't need to push this because we are pushing the real code.

## Babel Configuration
Create a file called `.babelrc` at the root of your project.  Add this to that file:
```js
{
  "presets": [
      "@babel/preset-env"
  ]
}
```
This file tells babel how to convert the JS in our project. We are using `@babel/preset-env` which is a preset list of ES6+ to ES5 conversion rules created by the babel team.  Their list is very thorough so no reason for us to reinvent the wheel and make our own conversion rules.

## Eslint Configuration
# Eslint
Eslint is a linting tool for javascript. Code linters are great tools because they often help you catch errors before your code runs.  They also help keep code bases consistent.  If everyone on a development team is following a certain set of rules about how their code should look then at the end of the day there will be a lot of consistency with how the code looks.

## VS Code
Want to be able to "see" your errors as you actually type them before webpack yells at you?  There is a sweet vs code extension out there called eslint.  Find it [HERE](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint).


## Setup Files
To use eslint we will need two different configuration files.

### .eslintignore
The `.eslintignore` file operates very similarly to the `.gitignore` file.  Anything that is in the `.eslintignore` file will be ignored from linting.  Currently you should have the following entries in your `.eslintignore`.
```js
webpack.config.js
node_modules
```

### .eslintrc
The `.eslintrc` file is where we configure our rules for eslint.  Your file should look like this:
```js
{
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module"
  },
  "extends": "airbnb-base",
  "globals": {
    "document": true,
    "window": true,
    "$": true,
    "XMLHttpRequest": true,
    "allowTemplateLiterals": true
  },
  "rules": {
    "no-console": [2, { "allow": ["error"] }],
    "no-debugger": 1,
    "class-methods-use-this": 0,
    "linebreak-style": 0
  }
}
```
# Configuring Webpack

## webpack.config.js
Once webpack is installed via npm packages all the configuration you need for it will go in a webpack.config.js file at the root.

This file should look like this:
```js
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  entry: './src/javascripts/main.js',
  module: {
    rules: [
      {
        enforce: "pre",
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
	options: {
          formatter: require('eslint/lib/cli-engine/formatters/stylish')
        }
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: true }
          }
        ]
      },
      {
        test: /\.scss$/,
        use: [
          MiniCssExtractPlugin.loader,
            { loader: 'css-loader', options: { sourceMap: true, importLoaders: 1 } },
            { loader: 'sass-loader', options: { sourceMap: true } },
        ],
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ['file-loader']
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: ['file-loader']
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  output: {
		path: __dirname + "/build",
		filename: "bundle.js"
	}
};
```

Lets go over each section of this file.
### Imports
Just like with our modules anything we need to import goes at the top of the file.  You will see that instead of import statements we are using `require`.  `require` and import do very similar things. `require` is generally used by things that are node.js based.  Import is generally used when using ES6 and behind the scenes when chrome/babel process the ES6 to ES5 they convert the `import` into a `require`.

The two libraries we are importing are `html-webpack-plugin` and `mini-css-extract-plugin`.  These two libraries are not required for webpack to do its thing but they are needed for the configuration we have.  So think of these imports as optional enhancements for webpack.

`html-webpack-plugin` is a plugin that is used to create html files.  This way we can have a template index.html file that includes any divs we need and then we let this plugin all in all the script and link tags our project needs.

`mini-css-extract-plugin` is a css extraction plugin.  We will be importing our SCSS into our javascript files (OMG crazy). This plugin searches through the javascript build and extracts out all the SCSS to create a css file that can then be linked to our index.html.

### Entry
The entry key is used to tell webpack where your entry point file is located.  Webpack by default has an entry point of `src/index.js`  but we are used to having an entry point of `main.js`.  So we can add the optional entry key to specify our own entry point.  We will sent the value of entry to the following path: `./src/javascripts/main.js`.
### Module
The module section of this file allows us to create a bunch of tasks for webpack to complete.  We do this by creating each task as an object and putting all the tasks in an array thats called rules.  Lets go through each of these tasks.

#### Eslint
```js
{
  enforce: "pre",
  test: /\.js$/,
  exclude: /node_modules/,
  loader: "eslint-loader"
}
```
This task happens before all other tasks run.  In this task we tell webpack to run eslint on ALL files with an extension `.js` that are not in the `node_modules` folder.  If this task comes back with any errors it fail the build process and none of the other tasks will run.  This means your code must be ERROR FREE in order for it to work.

#### Babel (ES6 to ES5)
```js
{
  test: /\.js$/,
  exclude: /node_modules/,
  use: {
    loader: "babel-loader"
  }
}
```
This task uses the `babel-loader` package to convert all of our ES6+ code into ES5 so old browsers (Internet Explorer) can run our code.  It converts any files that end in `.js` that are not in the node_modules folder.

#### HTML Bundling
```js
{
  test: /\.html$/,
  use: [
    {
      loader: "html-loader",
      options: { minimize: true }
    }
  ]
}
```
This task uses the `html-loader` package to copy over all of your html files to the `build` folder. It copies any files that end in `.html`.

#### SCSS to CSS
```js
{
  test: /\.scss$/,
  use: [
    MiniCssExtractPlugin.loader,
      { loader: 'css-loader', options: { sourceMap: true, importLoaders: 1 } },
      { loader: 'sass-loader', options: { sourceMap: true } },
  ],
}
```
This task uses the `sass-loader` package to convert any files that end in `.scss` to css.  Then it uses the `css-loader` package via the `MiniCssExtractPlugin` plugin to copy those css files over to the `build` folder.

#### Images
```js
{
  test: /\.(png|svg|jpg|gif)$/,
  use: ['file-loader']
}
```
This task uses the `file-loader` package to look for any image files (files that end in png, svg, jpg, or gif) and copies them over to the `build` folder.

#### Fonts
```js
{
  test: /\.(woff|woff2|eot|ttf|otf)$/,
  use: ['file-loader']
}
```
This task uses the `file-loader` package to look for any font files (files that end in woff, woff2, eot, ttf, or otf) and copies them over to the `build` folder.

### Plugins
The plugins section is where we configure third party plugins.  In our case we are configuring the two packages we are requiring into this file.

For the `html-webpack-plugin` package we need to give it the location of our `index.html` file and tell it what we want that file to be called when webpack builds this project (also `index.html`).

For the `mini-css-extract-plugin` package we need to tell it what to name the output files.  In this case we are use variable substitution `[name].css`.  This tells the plugin to name the file the same thing in the output it was named in the input.  So a file we write called `main.scss` will be converted from scss to css and then written as `main.css` in the `build` folder.

### Output
The output section tells webpack where to put code that it processes and what to call it.  In our configuration we tell webpack to make a folder called `build` at the root of our project and to call the minified javascript file `bundle.js`;

### Exporting
You will notice that all the above sections (entry, module, plugins, and output) are key/value pairs inside an object.  That object has a `module.exports` in front of it.  You can think of `module.exports` as the node.js equivalent of `export default {}`.  Webpack is looking for an object to be exported from the `webpack.config.js` file.  Whatever object it finds it uses for configuration.

# Frontend Dependencies
Up until this point we have always accessed front end libraries via CDN links.  This has some pros and cons.  Now that we are using webpack there is no reason for us not to use webpack to build those assets for us.  We already have webpack bundling our js file so we can easily use NPM to install things like bootstrap and then use webpack to bring those libraries into our projects.

## Installing Regular Dependencies
When we use frontend libraries we need to install them as dependencies NOT dev dependencies.  These libraries are REQUIRED in order to run the project.  We can install some common frontend libraries like this:

```sh
npm install axios bootstrap jquery popper.js --save
```
You should now see these four packages under the dependencies object in the package.json file.  Our package.json file knows to save this stuff as regular dependencies because of the `--save` flag at the end of the line.

We can then use regular import statements to start using these libraries in our projects.  Look below to see how we can import bootstrap into our code and start using it.

### Bootstrap
When using bootstrap we need to include 2 things - the js and the css.  To do this we need two different import statements.

For the CSS we will import the scss version of bootstrap into our main.scss file.  That import statement should be at the top of the file link this:
```
@import "~bootstrap/scss/bootstrap";
```

For the JS we can import the node_module into main.js with the following code:
```js
import 'bootstrap';
```

# Running Webpack
Before we start webpack we need to create some files.  Otherwise we will get some errors.  There is no need to add script or link tags for any of these files - webpack will do that for you.

## index.html
First we need to make an index.html.  This file need to live at `src/index.html`.  It should have some boilerplate code:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Test</title>
</head>
<body>
  
</body>
</html>
```

# main.scss
Since we are no longer writing in css we need to creat a main.scss file.  This file should live in `src/styles/main.scss`.  In this file change the background color of the body.
```css
body {
  background-color: blue;
}
```

## main.js
Next we need a main.js file because that is the entrypoint to our application.  This file should live at `src/javascripts/main.js`.  Since we can no longer add `console.log` statements add a `console.error` so we can see if things are working correctly.  We also need to import main.scss.
```js
import '../styles/main.scss';

console.error('hi');

```

## Just run the dang thing
Lets try running webpack!  We have created two scripts in our `package.json` file - they look like this:
```js
"scripts": {
  "start": "webpack-dev-server --mode development --open",
  "build": "webpack --mode production --module-bind js=babel-loader"
}
```
The scripts section of the package.json file is where you can write custom mini scripts to alias complex tasks - so we don't have to remember the complex parts.

### Development Mode
The top script is for development mode.  It starts the webpack development server (ie instead of making a physical build folder it makes that in memory).  To run this script type `npm start`.  This will start webpack and keep it running.  It will automatically open a browser to the correct url and your terminal will continuously update as you make code changes.

### Production Mode
The bottom script creates a production build of your code.  To run webpack in this way type `npm run build`.  After webpack finishes it will exit out to the command line and you should now see a folder called `build`.  This folder is now ready to be deployed the internet.  You should only need to run this version of webpack when you want to deploy stuff.  We will talk about how to deploy stuff in a few weeks.


# Including Images with Webpack
If you have a folder of local images that you want to load into your code things get a little strange with webpack.  Remember the only way webpack knows about assets is if they are imported into your javascript files.  Even our CSS is not added until those files are imported into our javascript files.  Below is some sample code for how to load a local image file into your project

```js
import cat from './assets/cat.jpg';

let domString = `<img src=${cat} alt="picture of a cat"/>`;

document.getElementById('cat').innerHTMl = domString;
```

# Axios

## Using Axios
> For every file you will need to make an XHR request in, you will need to require Axios
```js
import axios from 'axios';

const examplePromise = () => new Promise((resolve, reject) => {
  axios.get('http://localhost:3001/example')
    .then((data) => {
      resolve(data);
    })
    .catch((error) => {
      reject(error);
    });
});
```
