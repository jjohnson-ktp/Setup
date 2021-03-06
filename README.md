# Setup

## A collection of config and setup files to help maintain my sanity.

# Round 1

# JS Environment Setup in VS Code

## **Sources**:

1. PluralSight/Visual Studio Code:
   https://app.pluralsight.com/library/courses/visual-studio-code/table-of-contents

2. YT/The JavaScript Starter Kit Manifesto:
   https://www.youtube.com/watch?v=jubd2opc4Ps

3. PluralSight/Building a JavaScript Development Environment:
   https://app.pluralsight.com/library/courses/javascript-development-environment/table-of-contents

4. https://babeljs.io/

5. https://blog.angularindepth.com/configuring-typescript-compiler-a84ed8f87e3

6. https://www.39digits.com/configure-prettier-and-eslint-in-visual-studio-code/

7. https://www.npmjs.com/package/eslint-config-airbnb

8. https://kleopetrov.me/2016/03/18/everything-about-babel/

9. https://www.robinwieruch.de/react-eslint-webpack-babel/

## Language Features

VS Code gets much of its IntelliSense from _typings files_. These are TS files that set up expected structures/patterns for different code situations. VS Code comes packaged with a bunch of these, but we can extend the system with typings from the wider community.

1. First, download the Typings tool from npm: `npm i -g typings`. Test using `> typings -v`.
2. Get a wanted definition set, like angular: `typings install dt~angular --save`. This will create a `typings.json` config file and a `typings/` directory to house these definitions.

Search for typings using: `> typings search ...`

Why do this when we have tools like ESLint or TSLint? Because those tools are more specific and sometimes do not have what we need. There is nothing wrong with taking a little bit from all of them when putting together a project's tools. Just make sure that conflicts are resolved.

---

## JavaScript

### Package Security

Since any rando can publish to npm, security is a bit of a concern. This pain point gets alleviated by including **Node Security Platform (NSP)** as part of your workflow. We'll just install it for now and, later on, we'll see how to include it in automated npm tasks: `> npm i -g nsp`.

### Development Web Servers

We have several options:

1. **http-server**:

* Very simple, serves the current directory
* Live reload

2. **Express**

* Comprehensive and highly configurable
* Production-grade
* Can run anywhere

3. **Webpack Dev Server**

* Built directly into the Webpack bundler
* Serves from memory, as opposed to writing to disk. This makes it fast to see your changes.
* Supports _hot-reloading_, which means you can instantly see your changes on-screen, no matter how large.

4. **Browsersync**

* Sets up a dedicated IP for sharing work on LAN.
* All browsers on all devices stay in sync.
* Integrates into Webpack, Gulp, Grunt, Browserify, etc..

We'll do an example using **Express**:

In `server.js`,

```
let express = require('express');
let path = require('path');
let open = require('open');

let port = 3000;
let app = express();

app.get('/', function(req, res) {
  res.sendFile(path.join(__dirname, '../src/index.html'))
});

app.listen(port, function(err) {
  err ? console.log(err) : open('http://localhost:' + port);
});
```

Note that the code above requires an `index.html` file.

### Sharing Work

This avoid having to configure stuff like Azure, AWS, etc... to just share work from your local machine. Obviously, don't use these for production deployments.

**localtunnel**: `> npm i localtunnel`

* Allows sharing via a _public_ URL
* To use, add `> lt --port 3000` to your npm start script.

### Automation

Tools like **Grunt**, **Gulp**, **npm**, or your regular terminal are used to automated tasks. Just use **npm**/**yarn** (which uses the same packages.json file)

In `package.json`, you can place a `"scripts:"` field to, bascially, declare what you want npm to do when you hit `npm run __w/e__`:

```
...
"scripts": {
  "start": "node src/server.js"
}
...
```

You can run the above by typing `> npm start` or `> npm run start`. Notice that you can omit the 'run' portion of the command in this case. This only works if you're trying to run `start` or `test`.

npm also has something called _hooks_, which run at specific points. It's best seen via example:

```
...
"scripts": {
  "prestart": ....,
  "start": "node src/server.js"
  "poststart": ...,
  "security-check": "nsp check",
  "share": "lt --port 3000"
}
...
```

You can also run multiple commands simultaneously. When would you want to do this? An example would be times when you want to both start up an app and share it. Normally, you'd have to open 2 terminal sessions, one for each command, since each one takes over the terminal once run. npm can help us get around that limitation with `npm-run-all --parallel`. We can even write commands that we define separately, but within the same `scripts` object:

```
...
"scripts": {
  "prestart": ....,
  "start": "npm-run-all --parallel security-check open:src",
  "poststart": ...,
  "open-src": "node src/server.js"
  "security-check": "nsp check",
  "localtunnel": "lt --port 3000",
  "share": "npm-run-all --parallel open:src localtunnel"
}
...
```

---

## Beyond JS

There are various _supersets_ of JS that we can use to make development easier and more powerful. TypeScript, from Microsoft, is one of them. Another is basically JS, but in the future. Let's start from the ground; in case you don't know, JS is brought to you by the folks over at _Ecma International_. They're a <del>group of bureaucrats</del> standardization group that focus on specifications for stuff like JavaScript (technically called EcmaScript <del>...b/c bureaucrats</del>). They decide on the what goes into JavaScript/ES and doesn't belong. It's other peoples' jobs then to actually implement these changes in a way that allows the wider public to use it.

_A <del>bad</del> history lesson_:

* _Source_: https://en.wikipedia.org/wiki/ECMAScript

- _GitHub account_: https://github.com/tc39

- _caniuse_: https://caniuse.com/#search=es

1. Prior to December 1999, there is darkness.
1. In Dec 1999, the ES3 spec was released and browser vendors got to work implementing parts of it, little by little.
1. In Dec 2009 (yes, a decade later), ES5 came out because people got drunk, angry, and couldn't agree on what ES4 would be.
1. Only a little more than half a decade later, in June 2015, ES6 was released, which was so different from all the previous released that it broke the hearts and minds of the developers. It was basically a new language. It is backgrounds-compatible, so you could continue coding the old way. However, if you wanted to stay relevant, the language coding patterns in ES6, for solving the same problems as always, were almost totally different. So, yeah, pretty much a new language. As you can image, there was very slow browser adoption of the language. But, 2 years later, all browsers are at like 90% support for it!
1. ES7 was released in June 2016, and was easy on the shattered minds of its developers - it only added 2 new things to learn. There's almost no browser support for this yet.
1. Present day (Dec 13th, 2017): ES8 has been proposed, again in June of this year, but it won't be for a while longer. Definitely no browser support for this.

We'll just focus on ES6 for now, but you'll be able to guess how to get even the latest (experimental) features into your project, if you want to check them out.

A **transpiler** is used in a build (dev, qa, prod, w/e) to translate these JS supersets into a set that current-day browsers can understand. You don't have to memorize what browsers currently support or any of that. Just use a good transpiler and everything is taken care of. TypeScript uses _tsc_ to make \*.ts files into \*.js files that browsers can digest. Similarly, other solutions appeared to translate ES6 and many others into current-day JS. The biggest by far is called _Babel_.

### ES6 with Babel

**Important**: Despite what the main website might say, as of this writing, Windows machines need to install Babel _globally_, not locally. No idea why and no idea if this affects Mac or Linux users. This is what I found for Windows machines. Install it using `> npm i -g babel-cli`. This will hopefully not be the case soon and we can just use Babel locally. The presets that Babel will transpile to, however, can be installed locally as of now, i.e. on a per-project basis. The recommended preset is simply called `env`, as in `> npm i babel-env`.

Next comes configuration. This can be handled directly in package.json, but it is recommeded to do so in a separate file called `.babelrc`. This is where we can make use of the presets and, if you have them, the plugins installed (yes, Babel is highly cofigurable and extensible):

```
{
  "presets": ["env"]
}
```

Do a quick VS Code reload and start testing it out!

### TypeScript (TS)

The other JS superset that is taking the community by storm is Microsoft's OO language, TypeScript. Technically, it's a superset of ES6. It's starting to pop up everywhere, so it's worth familiarizing with as well. Like ES6, it needs to be transpiled down into a JS version that browsers can understand. This is done using the TS compiler, which can be acquired using `npm i -g typescript`.

Next comes configuration, using `tsconfig.json`. One thing to keep in mind about this file is that it tells tsc what your TS project root is:

```
{
  "compileOnSave": false,
  "compilerOptions": {
    "outDir": "./dist/",
    "baseUrl": "./src/",
    "sourceMap": true,
    "declaration": false,
    "moduleResolution": "node",
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,f
    "target": "es5",
    "typeRoots": ["node_modules/@types", "typings/*"],
    "lib": ["es2016", "dom"]
  }
}
```

If you want to specify vendor libraries outside of npm (idk why, but ok...), then you can tell tsc like this:

```
"compilerOptions": {
  "baseUrl": "./src/",
  "paths": {
    "*": [
      "libs/*"
    ]
  }
```

Setting this options **requires** you to also set the `baseUrl` option, which specifies the base directory to resolve non-relative modules in.

If `paths:` option is set, tsc goes through those folders and only checks `node_modules` if nothing is found. The **first** resolved module is used and no other paths are checked. So if you have a module placed inside both `node_modules` and `YourCustomFolder`, the module in your custom folder will be picked up by the compiler. If you need the compiler to use the module inside node_modules folder, add it to paths before your custom folder:

```
    "paths": {
      "*": [
        "*",
        "node_modules/*",
        "generated/*"
      ]
    }
```

### Both?

Why not? The `jsconfig.json` sets up the JS project root and Babel will only target \*.js files. The `tsconfig.json` config only targets the \*.ts.

If you want to be very specific about it, you can set a configuration option in `tsconfig.json` that tells the tsc compiler to only target specific files:

```
  "files": [
    "main.ts",
    "router/b.ts"
  ]
```

OR you can set a pattern:

```
  "include": [
    "router/*"
  ]
```

_Note_: tsc will also compile files that are referenced inside any file from that array.

In the case of conflicts, the priority is set in the following order:

1. Files
2. Exclude
3. Include

## Linting and Formatting

### JS/ES6 with ESLint and Prettier

`> npm i --save-dev eslint prettier`

We need guidelines to judge against and AirBnb's has become the de-facto standard: `> npm i --save-dev eslint-config-airbnb`. To use this styleguide, we need to install its peer dependancies: `> install-peerdeps --dev eslint-config-airbnb`. Finally, we want to run code through Prettier first, to format it, and pipe the results into ESLint for further processing using its automatic `--fix` flag: `> npm i prettier-eslint`.

All the software is now installed, so we're ready to configure. Create the `.eslintrc` file at the project root and grab the settings from my file (it's too long to include here).

Ok, let's now integrate all of this into VS Code iteself. Go to _User Settings_, search for `prettier.eslintIntegrations`, and set it to `true`. While there, set the following as well:

```
  "eslint.autoFixOnSave": true,
  "prettier.singleQuote": true,
  "prettier.trailingComma": "all",
  "prettier.eslintIntegration": true,
  "javascript.format.enable": false,
```

Some useful _plugins_ for ESLint:

1. JSON: `> npm i --save-dev eslint-plugin-json`
2. HTML/XML: `> npm i --save-dev eslint-plugin-html`

### TypeScript with TSLint and Prettier

You know how this goes by now. We're going to install tslint and and the package that makes it play nice with Prettier (which you should have installed earlier):
`> npm i -D tslint tslint-config-prettier`

To configure, create a `tslint.json` file and add:

```
{
  "extends": [
    "tslint:latest",
    "tslint-config-prettier"
  ]
}
```

Let's now get the best of both TSLint and ESLint with this package:
`> npm i -D tslint-eslint-rules`. Expand the `tslint.json` file like this:

```
{
  "extends": [
    "tslint:latest",
    "tslint-config-prettier"
    "tslint-eslint-rules"
  ],
  "rules": {
    "no-constant-condition": true
  }
}
```

---

# Coming Up

1. Bundling with Webpack
2. HTTP calls and mocking
3. View with React

<!-- 12. ReactJS support
//Runtime dependencies
> npm install --save react react-dom eslint-plugin-react
> install-peerdeps eslint-plugin-react
If you want to avoid ES2015 class syntax:
> npm install create-react-class
Add Babel support for ReactJS
> npm install babel-preset-react -->

# Afterwards

1. Adding test suites
2. Continuous integration
3. Automated workflows using npm scripts
