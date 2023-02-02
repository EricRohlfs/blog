# Form to table strategies 

Scenario: You have a table, where each row is editable. User clicks the edit button to open a form. User makes changes and hits save. 

Now you have to update the table with the information from the form.  Below are several very different strategies for updating the row.

1. You could update the whole table.
2. Direct DOM Manipulation: The form could manipulate the table (DOM) directly (VERY BAD IDEA).
3. Events: The form can throw events. The table can listen for these events. (Either built-in events, or custom events. Could be individual input element changes or watch for the form submit event.)
4. Functions: Form can call the table and pass in the data via a function call. The form can update the row with the data.
5. Proxy: Form can call the table or a table row, and update a data object (not a function). The data object can be wrapped in a proxy, the proxy intercepts the sets and updates the row.
6. Mutation Observer: Table can use Mutation Observer to watch the form, or even elements in the form. As the form changes, the mutation observer in the table can react to those changes and update the row information.

Todo: if I get some time I'll put examples, but with a bit of research on MDN you could figure these out.
