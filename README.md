# grunt-maven

**npm** tasks for [grunt-maven-plugin](http://https://github.com/allegro/grunt-maven-plugin).

**grunt-maven-plugin** NPM tasks power Maven+Grunt integrated workflow. These tasks depend on properties file produced
by grunt-maven-plugin, which contains paths and options derived from projects pom.xml.

## Getting started

Add plugin dependency to your **package.json**:

```js
"devDependencies": {
  "grunt-maven": "~1.1.0",
}
```

Load tasks in Gruntfile:

```js
grunt.loadNpmTasks('grunt-maven');
```

## mavenPrepare task

### Overview

This tasks role is to copy raw sources from Maven webapp sources to `target-grunt`, where Grunt tasks will be executed.
In your project's Gruntfile, add a section named `mavenPrepare` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  mavenPrepare: {
    options: {
      resources: ['**']
    },
    dev: {
    },
    prod: {
      resources: ['**', '!dev/**']
    }
  },
});
```

### Options

#### options.resources
Type: `Array`

List of patterns that will be evaluated using [minimatch](https://github.com/isaacs/minimatch) to choose resources that
will be copied from Maven webapp to target-grunt.

## mavenDist task

### Overview
`mavenDist` task copies artifacts produced by Grunt (less -> css, minified JS) from `target-grunt` to `target-grunt/dist`
and from `target-grunt` to Maven WAR directory.

```js
grunt.initConfig({
  mavenDist: {
    options: {
      warName: 'war',
      deliverables: ['**', '!non-deliverable.js'],
      gruntDistDir: 'dist'
    },
    dev: {
      warName: 'war-dev'
    }
  },
});
```

### Options

#### options.warName
Type: `String`

Name of WAR directory, residing in `/target/`, to which all deliverables will be copied.

#### options.deliverables
Type: `Array`

List of patterns that will be evaluated using [minimatch](https://github.com/isaacs/minimatch) to choose deliverables that
will be copied from `target-grunt/` to `target-grunt/dist` and to WAR.

#### options.workingDirectory
Type: `String`
Default: Directory where gruntfile is located

Deliverable patterns will be matched relative to this path, and all returned filepaths will also be relative to this path.

#### options.gruntDistDir
Type: `String`
Default: `dist`

Name of directory residing in `target-grunt`, where all deliverables will be copied.


## Using maven* tasks

Each defined task that wants to operate on resources from Maven, should start with `mavenPrepare` and end with `mavenDist`,
for example:

```js
grunt.registerTask('default', ['mavenPrepare', 'jshint', 'karma', 'less', 'uglify', 'mavenDist']);
```

* prepare environment in `target-grunt`
* run all Grunt specific tasks in `target-grunt`
* copy deliverables to WAR

### watch

Using `grunt-contrib-watch` can be very useful to create good development environment, just register whatever task
you should need to run on resources change, using properties generated by **grunt-maven-plugin** to obtain watch path:

```js
grunt.initConfig({
    gruntMavenProperties: grunt.file.readJSON('grunt-maven.json'),

    watch: {
        maven: {
            files: ['<%= gruntMavenProperties.filesToWatch %>'],
            tasks: 'default'
        }
    }
});

```

`gruntMavenProperties.filesToWatch` evaluates to `/src/main/webapp/statics_dir/**`. If you need to exclude some resources from
being watched (although you probably shouldn't need to), use `gruntMavenProperties.directoryToWatch`. This is simply path
to directory without globing pattern appended, in our case: `/src/main/webapp/statics_dir`.

## License

**grunt-maven-plugin** NPM tasks are published under [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0).
