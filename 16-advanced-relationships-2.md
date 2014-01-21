Advanced Model and Relationships - Part 2
==

Rendering todo lists
--

- Create a serializer for `TodoList` that includes the `todo_items`
- Create a todo item model/collection
- Have the collection take a todo list and scope its url appropriately
- Add the routes for an index method, and demonstrate that the `fetch` method
  works, and that the url for each model is now set as well

- Create the `todo_items` association in the model's constructor
- Create a `TodoItemsIndex` view, with a `ul` and render a `TodoItem` view for
  each element in the collection
- Have `TodoItem` just spit out the title
- Have the `TodoList` view add the appropriate class and render a
  `TodoItemsIndex`

- Move the height and width parts of .note.content back up to .body
  - Copy the width portion
- Add `margin: 0` to `.todo-list.todo-items`

- Make sure everything is rendering

Persisting todo lists
--

- Create a template for the todo item view, with a text input
- Bind to the change event
- Remove list styling
- Generalize all styles on the .note.content to apply to all inputs in .body
- Add a validate method to `TodoList` that checks the title or any todo item
  being valid
- Check that validation is working

- Add `accepts_nested_attributes_for` to `TodoList`
- Define `toJSON` to include the attributes
- Add to `permitted_parameters` (don't forget id)
- Check that persistence is working
