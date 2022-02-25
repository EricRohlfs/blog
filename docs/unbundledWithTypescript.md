# Working unbundled with Typescript

tldr; use importmaps

But why unbundled? Few reasons, my personal reason is I don't like waiting. Second, in some industries software supply chain management is a real thing. 
Frankly for smaller projects the paperwork involved can outweigh the benefits. 

The issue is the Typescript files will be in one folder ('src'). The output JavaScript will be in another ('dist'). 

You could have all imports point to the 'dist' folder, that kinda works, but the Typescript types are removed from the JavaScript in the dist folder, so we 
loose half the benefit of using Typescript. Using importmaps gives the best of both worlds. Typescript can follow the truncated or shorthand import statements, 
then use importmap to map the shorthand to the browser version of the JS files to import.

If you don't like this approach, you can always bundle with rollup, but then we are no longer working unbundled. 

Imports for my .ts files
```
  import { escapeHTML } from 'utils/encodeHTML'
  import { sortByType } from 'utils/sorter'
  import { waitForElement } from 'utils/reactive'
  import { SORTABLE_TH } from 'utils/table-sortableHeader'
```

Imports for my html files

```
<head>
  <!-- these are the children imports from the imports in the module script tag ( <script type="module"> ).
  <script type="importmap">
        {
          "imports": {
            "utils/sorter" : "/dist/utils/sorter.js",
            "utils/encodeHTML" : "/dist/utils/encodeHTML.js",
            "utils/reactive" : "/dist/utils/reactive.js",
            "utils/table" : "/dist/utils/table.js",
            "utils/table-sortableHeader" : "/dist/utils/table-sortableHeader.js",
            "utils/createElement": "/dist/utils/createElement.js",
            "table-uswds/movieAverageRatings":"/dist/table-uswds/movieAverageRatings.js"
          }
        }
    </script>
    <script type="module">
        import { defineCustomElements as defineMovieTable } from '../../dist/table-uswds/index.js'
        defineMovieTable()
        import {USHeader, USSideNavItem} from '/dist/layout/us-header.js'
        customElements.define('us-header', USHeader)
        customElements.define('us-sidenav-item', USSideNavItem)
    </script>
</head>

```
