#generator-angular-webpack

>Yeoman Generator for Enterprise Angular + WebPack Projects

Features

* Provides a directory structure geared towards large Angular projects.
    * Each controller, service, filter, and directive are placed in their own file.
    * All files related to a conceptual unit are placed together.  For example, the controller, HTML, LESS, and unit test for a partial are placed together in the same directory.
* Provides a ready-made Grunt build that produces an extremely optimized distribution.
   * Build uses [grunt-ngmin](https://github.com/btford/grunt-ngmin) so you don't have to use the Angular injection syntax for safe minification (i.e. you dont need `$inject` or `(['$scope','$http',...`.
   * `grunt serve` task allows you to run a simple development server with watch/livereload enabled.  Additionally, JSHint and the appropriate unit tests are run for the changed files.
* Integrates Bower for package management
* Includes Yeoman sub-generators for directives, services, partials, and filters
* Integrates LESS and includes Bootstrap via the source LESS files allowing you to reuse Bootstrap vars/mixins/etc.
* Easily Testable - Each sub-generator creates a skeleton unit test.  Unit tests can be run via `grunt test` and they run automatically during the grunt watch that is active during `grunt serve`.

Directory Layout
-------------
Below is an example of the folder structure.  In v3.0, all sub-generators for partials, services, directives, and filters, allow the user to specify where to save the new files.  Thus you can create your own directory structure (including nesting) as you desire.  In this example, the user has chosen to group the app into an `admin` folder, a `search` folder, and a `service` folder.


    app.less ....................... main app-wide styles
    app.js ......................... angular module initialization and route setup
    index.html ..................... main HTML file
    /admin ......................... example admin component folder
      /admin-directive1 ............ angular directives folder
        admin-directive1.js ........ example simple directive
        admin-directive1-spec.js.... example simple directive unit test
      /admin-directive2 ............ example complex directive (contains external partial)
        admin-directive2.js ........ complex directive javascript
        admin-directive2.html ...... complex directive partial
        admin-directive2.less ...... complex directive LESS
        admin-directive2-spec.js ... complex directive unit test
      /admin-partial ............... example partial
        admin-partial.html ......... example partial html
        admin-partial.js ........... example partial controller
        admin-partial.less ......... example partial LESS
        admin-partial-spec.js ...... example partial unit test
    /search ........................ example search component folder
      my-filter.js ................. example filter
      my-filter-spec.js ............ example filter unit test
      /search-partial .............. example partial
        search-partial.html ........ example partial html
        search-partial.js .......... example partial controller
        search-partial.less ........ example partial LESS
        search-partial-spec.js ..... example partial unit test
    /service ....................... angular services folder
        my-service.js .............. example service
        my-service-spec.js ......... example service unit test
        my-service2.js ............. example service
        my-service2-spec.js ........ example service unit test
    /img ........................... images (not created by default but included in /dist if added)
    /dist .......................... distributable version of app built using grunt and Gruntfile.js
    /bower_component................ 3rd party libraries managed by bower
    /node_modules .................. npm managed libraries used by grunt

Getting Started
-------------

Prerequisites: Node, Grunt, Yeoman, and Bower.  Once Node is installed, do:

    npm install -g grunt-cli yo bower

Next, install this generator:

    npm install -g generator-cg-angular

To create a project:

    mkdir MyNewAwesomeApp
    cd MyNewAwesomeApp
    yo cg-angular

Grunt Tasks
-------------

Now that the project is created, you have 3 simple Grunt commands available:

    grunt serve   #This will run a development server with watch & livereload enabled.
    grunt test    #Run unit tests.
    grunt build   #Places a fully optimized (minified, concatenated, and more) in /dist

When `grunt serve` is running, any changed javascript files will be linted using JSHint as well as have their appropriate unit tests executed.  Only the unit tests that correspond to the changed file will be run.

Yeoman Subgenerators
-------------

There are a set of sub-generators to initialize empty Angular components.  Each of these generators will:

* Create one or more skeleton files (javascript, LESS, html, spec etc) for the component type.
* Update index.html and add the necessary `script` tags.
* Update app.less and add the @import as needed.
* For partials, update the app.js, adding the necessary route call if a route was entered in the generator prompts.

There are generators for `directive`,`partial`,`service`, and `filter`.

Running a generator:

    yo cg-angular:directive my-awesome-directive
    yo cg-angular:partial my-partial
    yo cg-angular:service my-service
    yo cg-angular:filter my-filter

The name paramater passed (i.e. 'my-awesome-directive') will be used the file names.  The generators will derive appropriate class names from this parameter (ex. 'my-awesome-directive' will convert to a class name of 'MyAwesomeDirective').  Each sub-generator will ask for the folder in which to create the new skeleton files.  You may override the default folder for each sub-generator in the `.yo-rc.json` file.

Each sub-generator pulls the Angular app/module name from the package.json.  Therefore, if you choose to change the name of your Angular app/module, you must ensure that the name in the package.json stays in sync.

Sub-generators are also customizable.  Please read [CUSTOMIZING.md](CUSTOMIZING.md) for details.

Preconfigured Libraries
-------------

The new app will have a handful of preconfigured libraries included.  This includes Angular 1.2, Bootstrap 3, AngularUI Bootstrap, AngularUI Utils, FontAwesome 4, JQuery 2, Underscore 1.5, LESS 1.6, and Moment 2.5.  You may of course add to or remove any of these libraries.  But the work to integrate them into the app and into the build process has already been done for you.

Build Process
-------------

The project will include a ready-made Grunt build that will:

* Build all the LESS files into one minified CSS file.
* Uses [grunt-angular-templates](https://github.com/ericclemmons/grunt-angular-templates) to turn all your partials into Javascript.
* Uses [grunt-ngmin](https://github.com/btford/grunt-ngmin) to preprocess all Angular injectable methods and add the necessary Angular annotations to ensure minification will not break your app (and you don't have to use the array syntax to
manually add the annotations nor $inject).  Read more about [ngmin](https://github.com/btford/ngmin).
* Concatenates and minifies all Javascript into one file.
* Replaces all appropriate script references in `index.html` with the minified CSS and JS files.
* Minifies any images in `/img`.
* Minifies the `index.html`.
* Copies any extra files necessary for a distributable build (ex.  Font-Awesome font files, etc).

The resulting build loads only a few highly compressed files.

The build process uses [grunt-dom-munger](https://github.com/cgross/grunt-dom-munger) to pull script references from the `index.html`.  This means that **your index.html is the single source of truth about what makes up your app**.  Adding a new library, new controller, new directive, etc does not require that you update the build file.  Also the order of the scripts in your `index.html` will be maintained when they're concatenated.

Importantly, `grunt-dom-munger` uses CSS selectors to manage the parsing of the script tags. It is very easy to exclude certain scripts from the build.  For example, the project includes a references to the `livereload.js` from the `grunt-contrib-watch` task.  But this file should not be included in a production build.  Thus the `grunt-dom-munger` task is configured with a selector like `script[data-build!="exclude"]` and the script tag for `livereload.js` includes an attribute like `data-build="exclude"`.  You can use this flexibility in your project to include/exclude scripts in your production builds.

