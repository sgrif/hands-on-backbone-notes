Making it single page
==

Since we've gone through all this trouble to never actually leave the page, why
are we going through even more trouble to make it look like we are?

Styling
--

First let's make things look more appropriate.

- Change ul and li to divs
- 225x225, 20px margin, float left
- 1px black border
- 10px padding

- Remove dl and replace with input wrapped in header div
- height + line-height of 25 on div
- input is bold 17px font, no boder no background
  - no outline on :focus for all inputs

- add hr for separation
- no top margin, 10px bottom

- wrap content in a body textarea
- 165px tall, overflow: scroll, resize: none

Wiring up
--

- Create a save method, which grabs the title and body from the inputs
- Bind a change event with no target to that method
  - Mention that if you don't specify a target, the event gets triggered for any
    element inside of the view.
- Title is single line so it should save and blur when enter is pressed
- Bind the keypress event to call save if e.keyCode == 13
- Create an endEditing method, and call it at the end of save
  - `@$(':input').save()`

Improving the UX
--

It may not immediately be apparent how to save changes, so let's make it more
obvious.

- Create a beginEditing method and bind it to `click`
  - Add an editing class to @$el
    - `border-color: blue`
  - remove it in endEditing
  - `$('html').one('click', @save)` to detect click outside
    - Call `e.stopPropagation` to ensure that it isn't triggered immediately
    - Make sure `save` and `endEditing` use `=>`
      - Talk about differences between `->` and `=>`

- Add a footer div containing a submit button
  - `display: none`
  - Show in `beginEditing`
  - Hide in `endEditing`
  - `'click .done': 'endEditing'`

Adding custom events
--

This *almost* works, but if you click on one note, followed by another, it
breaks. We need something else to manage what is being edited, and have it
observe when it finishes.

- We can have the `EventsIndex` view manage what's being edited.
- First let's make sure it handles click events for the whole page.
- We don't really need the router any more, delete it and the edit view/template
- Change initialize to call `view.setElement('#container').render()`
- Make html and body full height, and make container full width/height

- Remove the `one` calls in beginEditing
- Add `@trigger('beginEditing')` and `@trigger('endEditing')`
- In the render loop for index, `listenTo` the `beginEditing` and `endEditing`
  events, keep track of the focused view, and call `endEditing` when something
  else begins editing
- Add a boolean around `isEditing` to ensure the event isn't triggered if we
  click the same div twice
- Finally, return false from both `beginEditing` and `endEditing`, to make
  sure the higher level click event isn't triggered unintentionally
  - Usually I prefer `e.preventDefault` here, but since we want to be able to
    call it manually, that isn't an option.

HERE THAR BE DRAGONS
--

- If we were still using the router, we would have just introduced a memory
  leak. We're binding to an event, and never unbinding it. We need to call
  `remove` on our view when we're done with it, and make sure we `stopListening`
  to the child view if it's destroyed, otherwise things will not be GC'd
- Mention that for complex projects, there's frameworks built on top of
  Backbone to help deal with issues like this, such as Marionette.
- It's relatively easy to introduce memory leaks in event driven applications
  such as this. They are a much bigger issue in an application such as this
  one, since we don't have a clean slate when changing pages. We'll talk more
  about how to track them down and how to deal with them later.

- Commit!
  - end single-page
