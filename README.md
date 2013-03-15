# [assemble-styles v0.2.3](http://github.com/assemble/assemble-styles)  [![Build Status](https://travis-ci.org/assemble/assemble-styles.png?branch=master)](https://travis-ci.org/assemble/assemble-styles)

> Compile your LESS stylesheets using underscore and JSON.

The project is current and under active development. We welcome contributions!

**Table of Contents**

- [Getting Started](#getting-started)
- [The "styles" task](#the-styles-task)
  - [Options](#options)
- [Examples](#usage-examples)
  - [Default Options](#default-options)
  - [Custom Options](#custom-options)
  - [Twitter Bootstrap](#twitter-bootstrap)
- [About](#about)
- [Contributing](#contributing)
- [Credit](#credit)
- [Release History](#release-history)


## Getting Started
This plugin requires Grunt `~0.4.1`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install assemble-styles --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('assemble-styles');
```

## The "styles" task
_Run this task with the `grunt styles` command._


Task targets, files and options may be specified according to the grunt [Configuring tasks](http://gruntjs.com/configuring-tasks) guide.
### Options

#### requires
Type: `String|Array`
Default: empty string

Specified files will be prepended to the beginning of src files, _not_ to the concatenated output. This feature is useful for "inlining" globaly-required LESS files, such as `variables.less` and `mixins.less`, so that _they do not need to be referenced with `@import` statements inside any individual files_.

#### concat
Type: `Boolean`
Default: true

Concatenate all source files by default. If you change the value to false, all source files will compile into individual files.

#### paths
Type: `String|Array`
Default: Directory of input file.

Specifies directories to scan for `@import` directives when parsing. Default value is the directory of the source, which is probably what you want. In other words, the `paths` option allows you to specify paths for your @import statements in the `styles` task, as an alternative to specifying a path on every @import statement that appears throughout your LESS files. So instead of doing this:

``` css
@import "path/to/my/less/files/mixins/mixins.less";
@import "path/to/my/less/files/bootstrap.less";
@import "path/to/my/custom/less/files/somewhere/else/custom.less";
```
you can do this:

``` css
@import "mixins.less";
@import "bootstrap.less";
@import "custom.less";
```

#### compress
Type: `Boolean`
Default: False

Compress output by removing some whitespaces.

#### yuicompress
Type: `Boolean`
Default: False

Compress output using cssmin.js

#### optimization
Type: `Integer`
Default: null

Set the parser's optimization level. The lower the number, the less nodes it will create in the tree. This could matter for debugging, or if you want to access the individual nodes in the tree.

#### strictImports
Type: `Boolean`
Default: False

Force evaluation of imports.

#### dumpLineNumbers
Type: `String`
Default: false

Configures -sass-debug-info support.

Accepts following values: `comments`, `mediaquery`, `all`.


### Usage Examples


#### Default Options

In your project's Gruntfile, add a section named `styles` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  styles: {
    options: {},
    files: {
      'path/to/result.css': ['path/to/source.less']
    }
  }
})
```

#### Custom Options

In this example, the `paths` and `requires` options are used:

```js
styles: {
  development: {
    options: {
      paths: ['src/less/files/'],
      requires: [
        'src/less/variables.less',
        'src/less/mixins.less'
      ]
    },
    files: {
      'dest/compiled.css': ['src/source.less']
    }
  },
  production: {
    options: {
      paths: ['assets/css'],
      yuicompress: true
    },
    files: {
      'dest/compiled.css': ['src/less/source.less']
    }
  }
}
```


#### Twitter Bootstrap

> Pick and choose which Bootstrap components you want to "bundle" or exclude.

A common (unjustified) complaint about Bootstrap is that it's bloated or has too many "extras", which really means: _"it's too much work to comment out or remove the @import statements I'm not using"_.

Well, lazy people rejoice! Because `assemble-styles` makes it easier than squeeze cheeze to customize Bootstrap.

(See [`bootstrap.json`](https://github.com/assemble/assemble-styles/blob/master/test/bootstrap.json)).

The included build "targets" are just examples to demonstrate how this plugin can make life easier.

``` js
styles: {
  // Global task options. Options can also be set for each target.
  options: {
    paths:    ['<%= bootstrap.base %>'],
    require: '<%= bootstrap.less.globals %>'
  },

  // Compile bootstrap.less
  bootstrap: {
    src:  '<%= bootstrap.lib %>',
    dest: 'test/css/bootstrap.css'
  },

  // Compile LESS "bundles" specified in ./test/bootstrap.json
  core: {
    src:  '<%= bootstrap.less.core %>',
    dest: 'test/css/core.css'
  },
  common: {
    src:  '<%= bootstrap.less.common %>',
    dest: 'test/css/common.css'
  },
  nav: {
    src:  '<%= bootstrap.less.nav %>',
    dest: 'test/css/nav.css'
  },
  zindex: {
    src:  '<%= bootstrap.less.zindex %>',
    dest: 'test/css/zindex.css'
  },
  misc: {
    src:  '<%= bootstrap.less.misc %>',
    dest: 'test/css/misc.css'
  },
  utilities: {
    src:  '<%= bootstrap.less.util %>',
    dest: 'test/css/utilities.css'
  },

  // Files object, a more compact way of building the same thing as above.
  bundles: {
    files: {
      'test/css/bundle/bootstrap.css': ['<%= bootstrap.lib %>'],
      'test/css/bundle/core.css':      ['<%= bootstrap.less.core %>'],
      'test/css/bundle/common.css':    ['<%= bootstrap.less.common %>'],
      'test/css/bundle/nav.css':       ['<%= bootstrap.less.nav %>'],
      'test/css/bundle/zindex.css':    ['<%= bootstrap.less.zindex %>'],
      'test/css/bundle/misc.css':      ['<%= bootstrap.less.misc %>'],
      'test/css/bundle/util.css':      ['<%= bootstrap.less.util %>']
    }
  },

  // Compile all targeted LESS files individually
  individual: {
    options: {concat: false },
    src:  '<%= bootstrap.less.all %>',
    dest: 'test/css/individual'
  },

  // Compile one LESS file, in this example "alerts.less"
  one: {
    src:  '<%= bootstrap.less.alerts %>',
    dest: 'test/css/single'
  },

  // Use minimatch pattern to build a list of LESS files,
  // then compile each file individually.  
  each: {
    options: {concat: false },
    src:  ['test/less/bootstrap/**/*.less'],
    dest: 'test/css/each'
  }
}
```

## About

`assemble-styles` is a gruntplugin for compiling LESS to CSS, but with a twist that you won't find in other similar plugins.  This plugin makes it much easier to maintain libraries of LESS components and themes, by leveraging JSON and underscore templates to enable you to define LESS "packages" or "bundles" using external configuration files.

The plugin is **quite simple to use**, and it demonstrates good conventions for managing your LESS components. But the best part is that you can easily switch back and forth between:
* compiling your LESS components _individually_, o
* concatentating all of your LESS files into a _singe file_

The best part is that your LESS projects will be easier to maintain.


## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).


## Authors

**Brian Woodward**

+ [http://twitter.com/doowb](http://twitter.com/doowb)
+ [http://github.com/doowb](http://github.com/doowb)

**Jon Schlinkert**

+ [http://twitter.com/jonschlinkert](http://twitter.com/jonschlinkert)
+ [http://github.com/jonschlinkert](http://github.com/jonschlinkert)


## Credit

`assemble-styles`, and some of the documentation on this page, is derived from [grunt-contrib-less](https://github.com/gruntjs/grunt-contrib-less), authored by [Tyler Kellen](https://github.com/tkellen). The plugin was modified to `concat` LESS files first, and then compile them into CSS files. This allows for prepending globally required LESS files, and it also adds the ability to build out individual CSS files, rathers than building a single conctatenated file.


## Release History

 * 2013-03-12   v0.2.0   Travis CI
 * 2013-03-08   v0.1.7   Many task improvements, enhanced bootstrap.json model. Greatly improved examples, readme updates.
 * 2013-02-27   v0.1.2   Add support for concat and requires options.
 * 2013-02-27   v0.1.0   First commit.

---


Grunt plugin authored by [Brian Woodward](http://github.com/doowb/)

*This file was generated on Fri March 14 2013.*

