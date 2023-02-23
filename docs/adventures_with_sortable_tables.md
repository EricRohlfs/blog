# Adventures with sortable table 

[Component Element Description](customElementDefinition.md)

# The interesting stuff

I had some interesting use cases and came up with solutions I have not seen in other places, either because the framework didn't lend itself to that kind of work or maybe others 
havn't had the same issues I have.

## High level layout 

```
 table
 - tHead - normal heading click to sort
 - tbody0 - optional column filtering input fields or can make it like excel with descrete values users can select
 - tbody1 - optional can be used for anything, but I'm using it for when a user clicks new, fills out the form and hits submit. Initially I show it at the top, here, but it goes away when the user sorts or filters and it just acks like any other row.
 - tbody2 - the main sortable tbody that has all the data. When a user clicks the heading to sort, or filters in tbody1, this is the area that gets updated/refreshed.
```

## Data

I don't want the table to re-render when a new item is added or updated in the table.  I find it annoying if I edit a row then it sorts off the page. I want to see the canges 
and can choose to have it go away with a separate filter or sorting action.
 
My first decision was to go with a push model vs. a pull model. My hope was this would provide a faster user experience over a pull model. 

My data model. Data falls down from master to filtered to sorted to paged. 

```
// dataMgr class
master:Arry<dataType> or main - think master data management
filtered:Array<dataType>
sorted:Array<dataType>
paged :Array<dataType>

// written in a functional way where data is copiedu
filter(filterFn, e:Event) // the previous fliterFn is cached and used again if master/main changes
sort(sortFn, e:Event) // is idempotent and can read the headrs for what is should be doing. Last sortFn is not cached
page(fagedFn, e:Event) // is idempotent and can read the paging api to figrue out what it needs todo. Last pageFn is not cached
// these items update master, which then calls the filter(),sort(),paged()
addItems(items:Array<dataType>)
addItem(item:dataType)
deleteItem(item:dataType)

// few other helper methods are exposed
```


