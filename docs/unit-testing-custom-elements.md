# Adventures in Unit Testing Custom Elements
2022-03-22

Primary choices are 
- jest
- karma
- https://open-wc.org/docs/testing/testing-package/

I did not try open-wc/testing.

I have done some puppeteer work, and jest-puppeteer is very nice. But that is a different kind of testing.



## Gotcha's

JSODM does not fully support customElements at the time of this writing.  Some Jest users are using the electron runner instead of JSDOM in jest. 
I could not get the electron plug-in worknig in Jest and I noticed that it's no longer supported.  You may want to double check.

I did get jest with jsdom working, and for some things it worked, but enough things did not work, so I moved on or back to Karma.

Install the karma-cli globally.  The karma cli is different than just karma

## My Package.json 
Not all dependencies in this list are required for Karma, as you can see, I have jest and puppeteer in there.

```
"devDependencies": {
 "@rollup/plugin-typescript": "^8.3.1",
    "@types/expect-puppeteer": "^4.4.7",
    "@types/jasmine": "^4.0.0",
    "@types/jest": "^27.4.0",
    "@types/jest-environment-puppeteer": "^5.0.0",
    "@types/puppeteer": "^5.4.4",
    "@typescript-eslint/eslint-plugin": "^5.10.2",
    "@typescript-eslint/parser": "^5.10.2",
    "cpr": "^3.0.1",
    "eslint": "^8.8.0",
    "eslint-plugin-jasmine": "^4.1.3",
    "jasmine": "^3.10.0",
    "jasmine-core": "^3.10.1",
    "jest": "^27.5.1",
    "jest-puppeteer": "^6.1.0",
    "karma": "^6.3.9",
    "karma-chrome-launcher": "^3.1.0",
    "karma-jasmine": "^4.0.1",
    "karma-jasmine-html-reporter": "^1.7.0",
    "karma-sourcemap-loader": "^0.3.8",
    "karma-spec-reporter": "^0.0.32",
    "npm-run-all": "^4.1.5",
    "puppeteer": "^13.1.3",
    "rollup": "^2.60.2",
    "sinon": "^11.1.2",
    "static-server": "^2.2.1",
    "terser": "^5.12.1",
    "ts-jest": "^27.1.3",
    "ts-sinon": "^2.0.2",
    "typescript": "^4.5.4"

```

## My Karma Setup

- typescript
- rollup
- jasmine
- I want source maps to work with my ts files.
- visual studio code
- I have separate configs for production code and karma tests.

In Visual Studio Code, I have three terminal tabs open. 
- terminal for git commands
- terminal for rollup to watch .ts files
- terminal for karma to watch dist/**/*.js files (but in my case really only needs to watch the ./dist/test/test_context.spec.js


## karma.config.cjs
karma.config.cjs - for sourcemaps to work with ts files. The rest of my config is default from karma init

```
 files: [
      // for sourcemaps and typescript. 
      { pattern: './src/**/*.ts', type: 'module', watched: false, served: true, included: false},
      { pattern: './dist/**/*.map', type: 'module', watched: false, served: true, included: false},
      
      // all my tests and code gets bundled up into this one file.  I would like to split out my tests from source code.
      // The primary issue in splitting is imports. Import statements in code do not have file extensions, and most static file servers like extensions (.js).
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


## Rollup.config
Notice I have two entries.  

- One is for 'production' or running in the browser. 
- The second one is for karma - it has the code already imported into it.

rollup.config.js 
```
import typescript from '@rollup/plugin-typescript'
export default [
  // this section is NOT used by Karma at all and is used by the browser when deployed for users
  {
    input: './src/bundlingExample/index.ts',
    output: {
      file: './dist/bundlingExample/index.bundled.js',
      format: 'es',
      sourcemap: true
    },
    plugins: [
      typescript({
        tsconfig: './tsconfig.rollup.json'
      })
    ]
  },
  // used only by Karma and has the tests and the code all the bundled up into one file.
  // It would be nice to split code from tests, but would need to work out imports, with either content negotation, importmaps, or some other plug-in. 
  {
    input: './src/test/test_context.spec.ts',
    output: {
      file: './dist/test/test_context.spec.js',
      format: 'es',
      sourcemap: true
    },
    plugins: [
      typescript({
        tsconfig: './tsconfig.rollup.json'
      })
    ]
  }
]


```

## tsconfig.rollup.json
I honestly don't know what is really needed from this config anymore.
I think it can be trimmed down.  I would start by removing the paths. They are there from a pre-typescript environment.

Add the "types" to your main tsconfig.json file too, or Visual Studio code will underline describe, beforeEach and it.

"SourceMap" is important for getting sourcemaps to work in Karma

"include" - notice I have the source code and my test code both included. You may be able to get away with one 'src' entry,
but in my sample project, I only want typescript to focus on one module (for now.)

```
{
    "compilerOptions": {
      "module": "es2020",
      "esModuleInterop": true,
      "target": "es2020",
      "moduleResolution": "classic",
      "sourceMap": true,
      "outDir": "dist",
      "lib": ["es2020", "dom", "dom.iterable"],
      "baseUrl":".",
      "paths":{
          "ts-sinon":["node_modules/ts-sinon/dist/"],
          "@sinon":["node_modules/@sinonjs/commons/types/index.d.ts"],
          "jasmine-core": ["node_modules/jasmine-core/lib/jasmine-core.js"]
      },
      "skipLibCheck":true,
      "types": ["jasmine", "sinon"]
    },
    "exclude":["./dist","node_modules"],
    "include": ["src/bundlingExample","src/test"],
  }
```

## test_context.spec.js
The primary purpose is to have tests and code all in one bundled file.

Notice I'm pointing at the index.spec.js file which is bundled up via rollup.  I should probably change the name to
index.spec.bundle.js for clarity.


```
import { MyFancyComponentTests } from "../dist/bundlingExample/index.spec.js";

MyFancyComponentTests()
```


## My test file

```
import { MyFancyComponent } from "src/bundlingExample/index"

// important! need to define the custom element or the browser will not be happy
customElements.define('my-fancy', MyFancyComponent)

export function MyFancyComponentTests() {
    describe('MyFancyComponent', function () {
        let fancy
        beforeEach(function () {
            fancy = new MyFancyComponent()
        })

        it('ariaSort= none removes the attribute', function () {
            fancy.ariaSort = 'none'
            const actual = fancy.getAttribute('aria-sort')
            expect(actual).toBe(null) // should not exist on the DOM
            expect(fancy.ariaSort).toBe('none') // should return 'none' from getter
        })
    })
}
```

## My source code

The source code is some junk I threw together for a quick demo, 

```
import { add } from "./add" // just a dependency to show how rollup works, it is exactly what you think it is and does.
import { ariaSortDirection } from "./types"

export class MyFancyComponent extends HTMLElement {

  constructor() {
    super()
  }

  connectedCallback() {
    this.ariaSort = 'none'
    // just showing how rollup puts imported items into one file.
    console.log(add(1, 2))

  }

  toggle() {
    this.classList.toggle('hide')
  }

  get ariaSort(): ariaSortDirection {
    let result = null
    if (this.hasAttribute('aria-sort')) {
      result = this.getAttribute('aria-sort') as ariaSortDirection
    }
    return (result == null) ? "none" : result
  }
  set ariaSort(val: ariaSortDirection) {
    if (val === 'none') {
      this.removeAttribute('aria-sort')

    } else {
      this.setAttribute('aria-sort', val)
    }
  }

}
```

## types.ts

Just some junk thrown together real quick for a live demo showing some things on types.

```
export interface IMyFancyComponent extends HTMLElement{
    toggle: ()=>void
    save: (user:userType)=>void
    ariaSortFancy: ariaSortDirection
}

export type userType = {
    firstName:string
    lastName:string
    address: {
        street:string
        city:string
        zip:string
        state:string
    }
}

/**
 * these are the only valid types according to the spec
 */
export type ariaSortDirection = 'ascending' | 'descending' | 'none' | 'other'

```



