Advanced Backbone.js Behavior
==

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
