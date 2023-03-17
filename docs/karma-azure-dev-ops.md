# Karma, code coverage, and azure dev ops 

There is quite a bit of info out there on wiring all this up for Angular and webPack projects, but not so much for the vanillaJs ES2022 customElement low tooling project.

The goal is to get a code coverage report to show in AzureDevOps pipelines.

Workingh backward:

* need an report file in the format AzDo accepts.  In this case I'm using Cobertura.
* In the azure pipeline yml file, need to point to the cobertura.xml file.
* Need to generate the cobertura.xml from the special karma.azure.conf.cjs file.
* Karma needs a rolled up file with the tests or the tests and the code. It uses ChromeHeadless to run the tests and 'coverage' reporter to generate the cobertura.xml file.
* Using rollup to make one file that has all the tests and the code under test. Might need to later break this up to get accurate code coverage reports.
* Rollup needs ONE file to rollup. All tests need to be exported via this file
* Since we are using typescript.  I have several typescript files. A main one for VSCode. Then the rest inherit from this and exclue various files, and in some cases have paths to jasmine and such.  

I'm sure I'm missing some parts or details. But this is all I can share right now. Good luck, hit me up with an issue on my blog github site if you have an issue.

## Tooling used/needed

* Karma
* Jasmine
* Sinon
* Rollup
* ChromeHeadless
* Typescript
* various karma plugins


## Generic package.json
```
{
  "name": "",
  "version": "1.0.0",
  "description": "",
  "main": "dist/src/index.js",
  "module": "dist/src/index.js",
  "browser": "dist/browser/index.js",
  "types": "dist/src/index.d.ts",
  "type": "module",
  "author": "Eric",
  "contributors": [],
  "scripts": {
    "build": "tsc --project tsconfig.json & rollup --config ./rollup.browser.cfg & rollup --config ./rollup.karma.cfg",
    "serve": "node static-server.cjs",
    "test:dev": "karma start karma.conf.cjs",
    "test:report": "karma start karma.azure.cfg.cjs",
    "karma": "karma start karma.conf.cjs",
    "rollup": "rollup --config ./rollup.browser.cfg",
    "rollup-watch": "rollup --config ./rollup.browser.cfg --watch",
    "rollup:test": "rollup --config ./rollup.karma.cfg",
    "rollup:test-watch": "rollup --config ./rollup.karma.cfg --watch",
    "artifacts-auth": "vsts-npm-auth -config .npmrc"
  },
  "repository": {
    "type": "git",
    "url": ""
  },
  "license": "ISC",
  "files": [
    "./src",
    "dist"
  ],
  "devDependencies": {
    "@custom-elements-manifest/analyzer": "^0.6.3",
    "@rollup/plugin-node-resolve": "^15.0.1",
    "@rollup/plugin-typescript": "^8.3.4",
    "@types/jasmine": "^4.0.0",
    "@types/node": "^17.0.29",
    "@typescript-eslint/eslint-plugin": "^5.33.0",
    "@typescript-eslint/parser": "^5.33.0",
    "del-cli": "^5.0.0",
    "eslint": "^8.21.0",
    "eslint-plugin-custom-elements": "^0.0.6",
    "eslint-plugin-jasmine": "^4.1.3",
    "eslint-plugin-no-unsanitized": "^4.0.1",
    "jasmine": "^3.10.0",
    "jasmine-core": "^3.10.1",
    "karma": "^6.4.1",
    "karma-chrome-launcher": "^3.1.0",
    "karma-coverage": "^2.2.0",
    "karma-jasmine": "^5.1.0",
    "karma-jasmine-html-reporter": "^1.7.0",
    "karma-requirejs": "^1.1.0",
    "karma-sinon": "^1.0.5",
    "karma-sourcemap-loader": "^0.3.8",
    "karma-spec-reporter": "^0.0.32",
    "rollup": "^2.60.2",
    "rollup-plugin-ts": "^3.2.0",
    "sinon": "^11.1.2",
    "static-server": "^2.2.1",
    "ts-node": "^10.9.1",
    "ts-sinon": "^2.0.2",
    "typescript": "^4.5.4"
  }
}

```

## karma.azure.conf.cjs
Might not need all of this, but after trying so many different things. It works and I'm moving on.

```
// Karma configuration
// How to configure in AzDo.
// the Cobertura report is in the coverage/cobertura.xml file
// https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/publish-code-coverage-results-v1?view=azure-pipelines

module.exports = function (config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '', //process.cwd(), //'', //require('path').join(__dirname, ''),

    // frameworks to use
    // available frameworks: https://www.npmjs.com/search?q=keywords:karma-adapter
    frameworks: ['jasmine', 'sinon'],
    // client: {
    //   clearContext: false // leave Jasmine Spec Runner output visible in browser
    // },

    // list of files / patterns to load in the browser
    files: [
      'dist/browser/test/index.js'
    ],

    // list of files / patterns to exclude
    exclude: [
    ],

    // preprocess matching files before serving them to the browser
    // available preprocessors: https://www.npmjs.com/search?q=keywords:karma-preprocessor
    preprocessors: {
      '**/*.js': ['sourcemap'],
      './dist/browser/test/index.js':['coverage']  // IMPORTANT FOR generating the report.
    },

    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://www.npmjs.com/search?q=keywords:karma-reporter
    //reporters: ['progress'],
    reporters: ['kjhtml', 'spec', 'progress', 'coverage'],
    coverageReporter: {
      reporters:[
        { type: 'html', subdir: 'report-html' },
        { type: 'lcov', subdir: 'report-lcov' },
        { type: 'cobertura', subdir: '.', file: 'cobertura.xml' },
        { type: 'text', subdir: '.', file: 'text.txt' },
        { type: 'text-summary', subdir: '.', file: 'text-summary.txt' },
      ],
      // lltype: 'text', //'html'
      dir: 'coverage/', // require('path').join(__dirname, '../coverage'),
      includeAllSources: true
    },

    // web server port
    port: 9876,


    // enable / disable colors in the output (reporters and logs)
    colors: true,


    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,


    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: false,


    // start these browsers
    // available browser launchers: https://www.npmjs.com/search?q=keywords:karma-launcher
    browsers: ['ChromeHeadless'],
    customLaunchers:{
      HeadlessChrome:{
        base: 'ChromeHeadless',
        flags: [
          '--no-sandbox',
          '--disable-web-security', '--disable-site-isolation-trials'
        ]
      }
    },

    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: true,

    // Concurrency level
    // how many browser instances should be started simultaneously
    concurrency: Infinity,

    mime: {
      'text/x-typescript': ['ts']
    },

  });
};

```

rollup.karma.cfg
``` 
//import typescript from '@rollup/plugin-typescript';
import {nodeResolve} from '@rollup/plugin-node-resolve';
import ts from 'rollup-plugin-ts';
export default [
  {
    input: './test/index.spec.ts',
    output: {
      file: './dist/browser/test/index.js',
      format: 'es',
      sourcemap: true
    },
    plugins: [
       nodeResolve(),
      ts({
        tsconfig: './tsconfig.karma.json'
      })
    ]
  }
];

```

tsconfig.karma.json
```
{
  "include": ["."],
  "exclude": [
    "./dist",
    "node_modules"
  ],
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "declaration": true,
    "declarationMap": true,
    "outDir": "./dist/browser/test",
    "noEmit": false
  },
  "paths": {
    "sinon": [
      "node_modules/sinon/pkg/sinon-esm.js"
    ],
    "ts-sinon": [
      "node_modules/ts-sinon/dist/index.ts"
    ],
    "@sinon": [
      "node_modules/@sinonjs/commons/types/index.d.ts"
    ],
    "jasmine-core": [
      "node_modules/jasmine-core/lib/jasmine-core.js"
    ]
  },
  "types": [
    "jasmine",
    "sinon"
  ]
}
```


azure-pipelines.yml
```

trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '18.x'

- script: |
    npm install
    npm run build
  displayName: 'npm install and build'

# NOTE: if tests are not built as part of build, set this up
# - task: Npm@1
#   displayName: 'build unit tests'
#   inputs:
#     command: 'custom'
#     customCommand: 'run rollup:test'

# Make sure karma is not in watch mode and this is the config that generates the report.
- task: Npm@1
  displayName: 'run unit tests'
  inputs:
    command: 'custom'
    customCommand: 'run test:report'

- task: PublishCodeCoverageResults@1
  displayName: 'Publish code coverage'
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: 'coverage/cobertura.xml'
```
