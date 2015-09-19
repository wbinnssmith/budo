## basics

budō allows you to get your scripts up and running quickly in a local environment. 

First, you will need [NodeJS and npm](http://nodejs.org/download/). Then you can install the tools globally:

```sh
npm install budo garnish -g
```

Now we can run budo to serve a file and start developing. Here we pipe the output to [garnish](https://github.com/mattdesl/garnish) for prettier colors in the terminal, but this is optional.

```sh
budo index.js | garnish
```

Open [http://localhost:9966/](http://localhost:9966/) to see the bundled result of `index.js`. 

<center><img src="http://i.imgur.com/a6lMvDY.png" width="80%"></center>

Saving `index.js` will be incremental, which means it will be fast even if your app spans hundreds of modules. 

## index.html

Notice we haven't had to write any HTML! If you want to, though, you can drop `index.html` in the same folder that you are serving budō from (or the base `--dir` folder), and it will use that instead of a dynamically generated index.

The `src` for your script tag should match the filename of the entry point you gave.

```html
<script src="index.js"></script>
```

You can specify a different end point for the server with a colon. This is useful for relative and absolute paths, for example:

```sh
budo /proj/foo/index.js:static/bundle.js | garnish
```

Now, you can use the following as your HTML:

```html
<script src="static/bundle.js"></script>
```

Also see the [`--serve` option](#multiple entries).

## local installation

If you are using these in your modules for demos/etc, you should save them locally so that others can get the same versions when they `git clone` and `npm install` your repo.

```sh
npm install budo garnish --save-dev
```

For local tools, we need to use [npm-scripts](https://docs.npmjs.com/misc/scripts). Open up your package.json and update `"scripts"` so it looks like this:

```sh
  "scripts": {
    "start": "budo index.js | garnish"
  },
```

Now running the following will start the development server:

```sh
npm run start
```

## live reload

budō also includes support for [LiveReload](livereload.com). The `--live` argument injects a script tag into your HTML file and listens for a live reload server.

```sh
budo index.js --live | garnish
```

Now when you save the `index.js` file, it will trigger a LiveReload event on your `localhost:9966` tab after watchify has finished bundling. It also listens to HTML and CSS reload, and injects stylesheets without a page refresh. 

From the command line, you can specify a filename glob to only trigger LiveReload in those cases. For example, to only allow CSS and HTML changes to trigger a LiveReload:

```sh
budo index.js --live=*.{html,css}
```

## multiple entries

Budo also supports multiple entry points; they will all get concatenated into a single bundle. If you aren't using a colon separator (`:`), the entry point will default to the first path. Or, you can explicitly set the path with the `--serve` option, as below:

```sh
budo test/*.js --serve static/bundle.js | garnish
```

<sup>*Note:* This uses unix glob expansion and may not work on Windows.</sup>

## browserify arguments

Everything after the `--` argument will not be parsed/manipulated, and will be passed directly to browserify. 

Currently, this is needed when using sub-arg syntax:

```sh
budo main.js --live -- -t [ foo --bar=555 --debug ]
```

## launch

To launch the browser once the server connects, you can use the `--open` or `-o` flag:

```sh
budo index.js --open | garnish
```

Also see [opnr](https://github.com/mattdesl/opnr), which allows for a similar functionality without forcing it as a command-line flag.

## `--onupdate`

In the CLI, you can run shell commands when the bundle updates using the `--onupdate` option. For example, to lint with [standard](https://github.com/feross/standard):

```sh
budo index.js --onupdate standard | garnish
```

This flag is only available in the command-line.

## internal IP

If you want LiveReload to work across many devices, you need to use your internal IP rather than `localhost`. 

With Unix shell, you can do this with [internal-ip](https://www.npmjs.com/package/internal-ip).

```sh
# make sure we have the tool
npm install internal-ip -g

# run using internal IP
budo index.js --host=`internal-ip` | opnr | garnish
```

<sup>*Note:* This uses unix features and may not work in Windows.</sup>