# Form to table strategies 

Scenario: You have a table, where each row is editable. User clicks the edit button to open a form. User makes changes and hits save. 

Now you have to update the table with the information from the form.  Below are several very different strategies for updating the row.

1. You could update the whole table.
2. Direct DOM Manipulation: The form could manipulate the table (DOM) directly (VERY BAD IDEA).
3. Events: The form can throw events. The table can listen for these events. (Either built-in events, or custom events. Could be individual input element changes or watch for the form submit event.)
4. Functions: Form can call the table and pass in the data via a function call. The form can update the row with the data.
5. Proxy: Form can call the table or a table row, and update a data object (not a function). The data object can be wrapped in a proxy, the proxy intercepts the sets and updates the row.
6. Proxy via state manager: .... todo
7. Mutation Observer: Table can use Mutation Observer to watch the form, or even elements in the form. As the form changes, the mutation observer in the table can react to those changes and update the row information.

Todo: if I get some time I'll put examples, but with a bit of research on MDN you could figure these out.

Number 2 Direct Dom Manipulation and Number 7 Mutation Observer are the worst. No separation of concerns. Both components have to know too much about each other. A change to one means a change to both.

Numbers 3,4,5 are all fine and about the same, but if I had to choose a best to worst ...

Lets discuss the trade offs.

Events have the best separation of concerns, especially if the form is throwing custom events. That way the form does not have to know about the table (like it would in a function or proxy object scenario. Unless if a proxy object scenario the proxy was passed into the form for processiong and the form didn't over write it.)
But depending on the events used (custom or built-in) we may have to write 'extra' code to publish the event (it's usually not much.)
And theoretically if there are changes, we may not know who is listening and responding to our event (except we have code editing tools with find so we could find out pretty quick.) 

Functions are very easy to reason about.  We would know that the form needs to update the table, because the form would have to query for the table, or have a reference passed in when the from was spun up. You could argue this is too tight of coupling, but really? Show me some evidence where this would be a real world problem. This is the web, it is already sloppy.

Proxy is fine too, but unlike a function, if you don't know proxy objects it can feel like black magic! And there is a risk the form programmer might overwrite the proxy, but that is a bit silly and would be corrected quickly in development. You could argue that if the proxy object has to change like from firstName to fName, the function method is less tighly coupled and easier to reason about for fixing those types of issues.

The one nice thing about the mutation observer is the form knows nothing about the table! The form doesn't need to create a custom event. Bult-in events are about the same.  But then the table knows too much about the form. So that is not idea either.

A quick note on a proxy with stateMgr. A state manager is usually global, so both components only need to know about the state manager. But 
honestly it feels like brining your mom on a first date.  And it is just a bunch of extra work compared to the form just querying for the table and calling a function is
so simple.  But if the form had to update three tables and a graph on a page.  I would much rather use a state manager. Then it would be worth it.







