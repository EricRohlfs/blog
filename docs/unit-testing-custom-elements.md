# Adventures in Unit Testing Custom Elements
2022-03-22

## Gotcha's

JSODM does not fully support customElements at the time of this writing.  Some Jest users are using the electron runner instead of JSDOM in jest. 
I could not get the electron plug-in worknig in Jest and I noticed that it's no longer supported.  You may want to double check.

I did get jest with jsdom working, and for some things it worked, but enough things did not work, so I moved on or back to Karma.

Install the karma-cli globally.  The karma cli is different than just karma

## My Karma Setup

- typescript
- rollup
- jasmine
- I want source maps to work with my ts files.
- visual studio code

In Visual Studio Code, I have three terminal tabs open. 
- terminal for git commands
- terminal for rollup to watch .ts files
- terminal for karma to watch dist/**/*.js files (but in my case really only needs to watch the ./dist/test/test_context.spec.js


karma.config.cjs - for sourcemaps to work with ts files. The rest of my config is default from karma init

```
 files: [
      // for sourcemaps and typescript. 
      { pattern: './src/**/*.ts', type: 'module', watched: false, served: true, included: false},
      { pattern: './dist/**/*.map', type: 'module', watched: false, served: true, included: false},
      
      // all my tests and code gets bundled up into this one file.  I would like to split out my tests from source code.
      // The primary issue in splitting is imports. Imports do not have file extensions and most browsers like extensions.
      // It would be nice to setup content negotation on the web server to recognize the mime-type and map that to the .js file.
      // my wish for a content negotation plug-in  :
      //  request uri: ./app/index
      //  request mime-type: 'text/javascript'
      //  response returns: ./app/index.js
      { pattern: './dist/test/test_context.spec.js', type: 'module', included: true, served:true, watched:true }
    ],
  mime: {
      'text/x-typescript': ['ts']
  },
  // I'm not sure if the preprocessor is needed anymore, have net tested it without it.
  preprocessors: {
    '**/*.js': ['sourcemap']
  },
```


