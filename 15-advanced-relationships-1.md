Advanced Models and Relationships - Part 1
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
