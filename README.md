# grunt-closure-compiler

A Grunt task for [Closure Compiler](https://developers.google.com/closure/compiler/).

## Getting Started

First you need to download a [build of Closure Compiler](http://code.google.com/p/closure-compiler/downloads/list) or build it [from the source](http://code.google.com/p/closure-compiler/source/checkout) (see [details below](#closure-compiler-installation-from-source)).

Optionally, you can set up an environment variable called `CLOSURE_PATH` that points to your Closure Compiler dir (see [details below](#set-up-the-environment-variable)).

Install this module on your project's [grunt.js gruntfile](https://github.com/cowboy/grunt/blob/master/docs/getting_started.md):
```bash
$ npm install grunt-closure-compiler
```

Then register the task by adding the following line to your `grunt.js` gruntfile:
```javascript
grunt.loadNpmTasks('grunt-closure-compiler');
```

Then you can minify JavaScript calling:
```javascript
grunt.initConfig({
  'closure-compiler': {
    frontend: {
      closurePath: '/src/to/closure-compiler',
      js: 'static/src/frontend.js',
      jsOutputFile: 'static/js/frontend.min.js',
      maxBuffer: 500
      options: {
        compilation_level: 'ADVANCED_OPTIMIZATIONS',
        language_in: 'ECMASCRIPT5_STRICT'
      }
    }
  }
});
```

`closurePath` is required if you choose not to set up the `CLOSURE_PATH` environment variable. In this case, it should point to the install dir of Closure Compiler (not the subdirectory where the `compiler.jar` file is located).

`js` property is always required.

If `jsOutputFile` property is set, the script will be minified and saved to the file specified. Otherwise it will be output to the command line.

`maxBuffer` property

If the buffer returned by closure compiler is more than 200kb, you will get an error saying "maxBuffer exceeded". To prevent this, you can set the maxBuffer to the preffered size you want (in kb)

Optionally, several parameters can be passed to `options` object.

## Documentation

### Closure Compiler installation from source

Install dependencies:
```bash
$ sudo apt-get install git ant openjdk-6-jdk
```

Then checkout the source from Git and build:
```bash
$ git clone https://code.google.com/p/closure-compiler/
$ cd closure-compiler
$ ant
```

To refresh your build, simply call:
```bash
$ git pull
$ ant clean
$ ant
```

#### Mac

Mac users can install it from brew:
```bash
$ brew install closure-compiler
```

### Set up the environment variable

Setting up a `CLOSURE_PATH` environment variable is preferred because:

* You don't have to specify the `closurePath` each time.
* It makes it easy to use contributed externs.

In case you're wondering, Closure Compiler utilizes continuous integration, so it's unlikely to break.

If you create the `CLOSURE_PATH` environment variable, make sure to have it pointing to the `closure-compiler` dir created earlier (and not to the `build` subdirectory where the jar is located).

#### Mac

On Mac, when installed with brew, you can get the install path using:
```bash
$ brew --prefix closure-compiler
/usr/local/Cellar/closure-compiler/20120710
```

Just append `/libexec` to what you get. In this example, you should use the following path:
```
/usr/local/Cellar/closure-compiler/20120710/libexec/
```

### `js` property

This task is a [multi task](https://github.com/cowboy/grunt/blob/master/docs/types_of_tasks.md), you can specify several targets. The task can minify many scripts at a time.

`js` can be an array if you need to concatenate several files to a target.

You can use Grunt `<%= somePropInitConfig.sub.sub.prop %>` or `*` based syntax to have the file list expanded:
```javascript
grunt.initConfig({
  'closure-compiler': {
    frontend: {
      js: 'static/src/frontend.js',
      jsOutputFile: 'static/js/frontend.min.js',
    },
    frontend_debug: {
      js: [
        '<%= closure-compiler.frontend.js %>',
        // Will expand to 'static/src/frontend.js'
        'static/src/debug.*.js'
        // Will expand to 'static/src/debug.api.js',
        //   'static/src/debug.console.js'...
      ],
      jsOutputFile: 'static/js/frontend.debug.js',
      options: {
        debug: true,
        formatting: 'PRETTY_PRINT'
      }
    },
  }
});
```

### `options` properties

Properties in `options` are mapped to Closure Compiler command line. Just pass options as a map of option-value.

If you need to pass the same options several times, make it an array. See `define` below:
```javascript
grunt.initConfig({
  'closure-compiler': {
    frontend: {
      js: 'static/src/frontend.js',
      jsOutputFile: 'static/js/frontend.min.js',
      options: {
        compilation_level: 'ADVANCED_OPTIMIZATIONS',
        language_in: 'ECMASCRIPT5_STRICT',
        define: [
          '"DEBUG=false"',
          '"UI_DELAY=500"'
        ],
      }
    }
  }
});
```

When defining externs, if you added the `CLOSURE_PATH` environment variable you can easily reference Closure Compiler builtin externs using `<%= process.env.CLOSURE_PATH %>` Grunt template:
```javascript
grunt.initConfig({
  'closure-compiler': {
    frontend: {
      js: 'static/src/frontend.js',
      jsOutputFile: 'static/js/frontend.min.js',
      options: {
        externs: '<%= process.env.CLOSURE_PATH %>/contrib/externs/jquery-1.7.js',
      }
    }
  }
});
```

Otherwise, use the `<%= %>` Grunt template:
```javascript
grunt.initConfig({
  'closure-compiler': {
    frontend: {
      closurePath: '/src/to/closure-compiler',
      js: 'static/src/frontend.js',
      jsOutputFile: 'static/js/frontend.min.js',
      options: {
        externs: '<%= closure-compiler.frontend.closurePath %>/contrib/externs/jquery-1.7.js'
      }
    }
  }
});
```

To specify boolean options (such as `process_common_js_modules`, i.e. no value are required), set its value to `undefined` (or `null`):
```javascript
grunt.initConfig({
  'closure-compiler': {
    frontend: {
      js: 'static/src/frontend.js',
      jsOutputFile: 'static/js/frontend.min.js',
      options: {
        process_common_js_modules: undefined,
        common_js_entry_module: 'exports'
      }
    }
  }
});
```

## Note

grunt-closure-compiler development was founded by [Dijiwan](http://www.dijiwan.com/). Our team uses it on a daily basis to minify our frontend JavaScript.

The directory structure was inspired by [grunt-less](https://github.com/jharding/grunt-less), a Grunt task for Less.

## License

Copyright (c) 2013 Guillaume Marty

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
