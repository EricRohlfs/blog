# Building a sortable, pagable table using custom elements

tldr: Use multiple components vs. one big monolithic component. Multiple components is more verbose (more lines of code), but the separation of concerns significantly improves the reasoning of the code and the ability to re-use the code.

The big takeaway for me was to not build one big component, but to create specialized custom elements.


The TableSortbleElement is fairly dumb, it mainly knows how to sort and create elements from a json object
- create header elements from a json cfg object
- create table cells from a json cfg object
- sort the backing json object and re-paint the page
- work with the cusomElement TableSortableHeaderElement to manage the ascending, descending, none UI as described by https://designsystem.digital.gov/components/table/

### Components Created

- export class MoviesTable extends TableSortableElement
- export class TableSortableElement extends HTMLTableElement
- export class TableSortableHeaderElement extends HTMLTableCellElement implements ISortableTableCellElement
- export class MovieAverageRatingsCellElement extends HTMLTableCellElement 
- export function addBasicCellToRow

MovieTable and MovieAverageRatingsCellElement are specifically for the movies table, the rest are re-usable components and can be used in any table.

MovieAverageRatingsCellElement 'td' is more complex so it gets its own custom element, the rest of the 'td's are created using the addBasicCellToRow method,

### Code

I'll try to post the code, but for now, here are a few key item that might help reason about the implementation.

I may restructure this to allow for config options for the table itslef, the structure below works for the rows, but not for setting any global defaults

```
  // Typescript
  export type sortableSetup = {
    jsonKey: string // name of the key/field in the object
    headerText: string // what to show in the table
    callback?: Function
    sortOnJsonKey: string // the name of the key/filed to sort on
    ariaLabel?: string
    tableCellAttributes?: any // json object
    tableHeaderAttributes?: jsonType
    addTableCellCallback: (addTableCellCallBackType)
}

  tableConfig(): sortableSetup[] {
        return [
            { jsonKey: 'title', headerText: 'Title', sortOnJsonKey: 'title', ariaLabel: 'Title of movie', addTableCellCallback: addBasicCellToRow },
            { jsonKey: 'year', headerText: 'Year', sortOnJsonKey: 'year', ariaLabel: 'Year move was released', addTableCellCallback: addBasicCellToRow },
            { jsonKey: 'averageRating', headerText: 'Average Rating', sortOnJsonKey: "averageRating", ariaLabel: 'average rating', tableCellAttributes: { is: MovieEleDefinitionName }, addTableCellCallback: this.createAveragesCell }
        ]
    }
```


