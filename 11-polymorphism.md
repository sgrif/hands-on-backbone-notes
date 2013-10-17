Polymorphism
==

Now that we've refactored notes to be their own model, let's add todo lists.

- `rails g model TodoList`
- Extract the relation to `Note` and the touch callback to a concern
- `rails g model TodoItem todo_list:belongs_to title:string complete:boolean`
- Default `complete` to false
- Add `dependent: destroy` and `touch: true`

- Update the seeds file to have one note, and one todo list with 2 items
- `rake db:reset`

Polymorphism in the front-end
--

- The page is crashing as soon as we try to parse a todo list
- We can solve this in the short term by having
  `data.content = data.body.sticky_note?.content || ""`

- The page is now loading, but has an empty text field rather than a todo list
- Create an empty `TodoList` model
- Rename `Note` to `StickyNote`
- Change model in the items collection to be a function
  - Return a new instance of a model from this function
- Note that the model now has the correct class

- Extract body of `Views.Note` to a separate class
- Create a `TodoList` view, and have its render method just put some placeholder
  text
- Define a `viewFor(model)` method on `ScratchPad` that grabs the correct view
  class, and returns a new instance
- We're now loading the correct view

- The add note button is broken.
- Change the `addNote` method to pass a new `StickyNote`
- Everything should be working again

- Commit!
  - end polymorphic-rendering

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

Completing todos
--

- Add complete to the jbuilder partial if it's not already there
- Add a `setDisabled` method to the view, which sets disabled if the model is
  complete
  - I don't think that this kind of logic belongs in a dumb template, and if we
    put it there, we'd have to rerender the whole thing to get the changes
- `@listenTo(@model, 'change:complete', @setDisabled)`
- Add a line through disabled inputs
- Bind to the change event on the checkbox
- Set the checked attribute in `setDisabled`
- Add `complete` to the allowed parameters

- We coded ourselves into a corner with the click events
  - Change index click event to only run if the clicked element isn't a child of
    a `.scratch-pad-item`
  - Change the item click event to return true
- Check that everything is working

- Commit!
  - End saving-todos

Adding new todos
--

- The easiest way we can get a simple interface is just to always render a todo
  view with a new item on the bottom
- Append a new item in the constructor of the items view

- Unfortunately, this won't trigger at present, because we aren't returning the
  whole resource when we save
- Create a show view, and break things out into the appropriate partials

- In the model, continue changing the body in initialize, but listen to a change
  event, and update the associated model

- Update item and note views to only save when the specific inputs change
- Override save in itemBody to save the parent

- In the items index view, listen for change id and add a new todo
