# Django-ReactJs-Boilerplate
A boilerplate for using django and reactjs with webpack
## Creating a Django project
Ensure you have activated the virual environment where you have installed Django and other requirements.
Run `django-admin startapp djbackend` in the terminal to create the django project. I called mine djbackend.
## Setting up webpack
We will be using webpack and Babel for our React app instead of create-react-app.
We'll use npm to manage our frontend dependencies. Ensure you have NodeJs installed in your system.
Let's generate a package.json file in our project root by running `npm init` in the terminal.
### Webpack
Is a module bundler which lets us bundle our project files into a single file.
##### Installing Webpack
We install webpack, webpack-cli and webpack-bundle-tracker through the following npm command.`npm install webpack webpack-bundle-tracker webpack-cli --save-dev`.
We installed webpack-cli so that we can use webpack in the command line.
To use the webpack dev server, we install it through `npm install webpack-dev-server --save-dev`.
##### Installing React
To install react for our front-end developmend we run `npm install react react-dom` in the terminal.
##### Installing Babel
This command installs babel and its other dependencies that we need. `npm install @babel/core @babel/preset-env @babel/preset-react babel-loader --save-dev`
Let's create a .babelrc file and add the following contents, ```{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}```
### Create Directories
Let's create an assets directory to hold our frontend logic that will be bundled into a js file and served to Django.
`mkdir -p assets/reactjs`
`touch webpack.config.js`
`touch assets/js/index.js`

#### Create Webpack config
Let's create a webpack.config.js file that will enable us to load .jsx files using babel and use webpack-bundle tracker
plugin to extract information to webpack-stats.json file
