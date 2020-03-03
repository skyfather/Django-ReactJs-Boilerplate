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

### Intergrating with Django
`pip install django-webpack-loader`. This package will enable Django to load the latest bundle for our site.

We'll need to add/modify the following in our settings.py file.

```
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'assets'), # We do this so that django's collectstatic copies or our bundles to the STATIC_ROOT or syncs them to whatever storage we use.
)
```
```
WEBPACK_LOADER = {
    'DEFAULT': {
        'BUNDLE_DIR_NAME': 'bundles/',
        'STATS_FILE': os.path.join(BASE_DIR, 'webpack-stats.json'),
    }
}
```
```
INSTALLED_APPS = (
 ...
 'webpack_loader',
)
```
```
TEMPLATES = [
    {   ...
        'DIRS': [os.path.join(BASE_DIR,'djbackend/templates')],
        ...
    },
]
```
Create a templates directory and add the following templates to it. I called it base.html in this case.
```
{% load render_bundle from webpack_loader %}
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Django-React</title>
  </head>

  <body>
    <div id="react-app"></div>
    {% render_bundle 'main' %}
  </body>
</html>
```

In the projects url file, modify it to look like this:
```
from django.contrib import admin
from django.urls import path
from django.views import generic

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', generic.TemplateView.as_view(template_name='base.html')),
]

```
### Webpack Configuration
Create a webpack.config.js file and add the followwing contents to it as appropriate.
```
var path = require("path")
var webpack = require('webpack')
var BundleTracker = require('webpack-bundle-tracker')

module.exports = {
  context: __dirname,

  entry: [
      'webpack-dev-server/client?http://localhost:3000',
      'webpack/hot/only-dev-server',
      './assets/reactjs/index', // entry point of our app. assets/js/index.js should require other js modules and dependencies it needs
  ],

  output: {
      path: path.resolve('./assets/bundles/'),
      filename: "[name]-[hash].js",
      publicPath: 'http://localhost:3000/assets/bundles/', // Tell django to use this URL to load packages and not use STATIC_URL + bundle_name
  },

  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoEmitOnErrorsPlugin(), // don't reload if there is an error
    new BundleTracker({filename: './webpack-stats.json'}),
  ],

  module: {
    rules: [
      { 
        test: /\.jsx?$/, 
        exclude: /node_modules/, 
        loaders: ["babel-loader"] // to transform JSX into JS
      }, 
    ],
  },

  devServer: {
    contentBase: './assets/reactjs',
    hot: true
  },

  resolve: {
    modules: ['node_modules', 'bower_components'],
    extensions: ['.js', '.jsx']
  },
}
```

Create a server.js file and the following content's to it
```
var webpack = require('webpack')
var WebpackDevServer = require('webpack-dev-server')
var config = require('./webpack.config')

new WebpackDevServer(webpack(config), {
  publicPath: config.output.publicPath,
  hot: true,
  inline: true,
  historyApiFallback: true
}).listen(3000, '0.0.0.0', function (err, result) {
  if (err) {
    console.log(err)
  }

  console.log('Listening at 0.0.0.0:3000')
})
```

Now that we are done, you can run `node server.js` and open another terminal and run `python manage.py runserver`.
Or optionally you can modify the package.json file like this
```
"scripts": {
    "build": "webpack --config webpack.config.js --progress --colors --mode=development",
    "watch": "node server.js"
  },
```
And run npm run watch.
