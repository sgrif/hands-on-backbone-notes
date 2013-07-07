Adding and Removing
==

We've built a great bit of functionality for editing, but we've forgotten to
give the user the ability to add new items, and delete them once they're there.

Adding add
--

- `div.add-actions>p>button`, give give the div the same styles as `.note`
  except for the border
- Make the container `position: relative`
- Absolutely position the `p`, and vertically center it
  - `
    p {
      position: absolute;
      top: 50%;
      height: 3em;
      margin-top: -1.5em;
    }
    `
- Make the button say 'Add Note'
- Add placeholders to the inputs

- Bind the click event to a method called `addNote`
- Call `add` on `@collection` (must pass an empty object or it's noop)`
- Call `@render`

This feels wrong. We're re-rendering the whole page, when really we only have a
single div that needs to get placed. Plus we've introduced a memory leak from
the event bindings that I mentioned earlier.

- Move the render loop to a separate method and pass that to `each`
  - I refactored changing the focused view to a method, and called it with null
    in the click event
- Remove the call to render, and instead bind to collection add in the
  constructor
  - `renderModel` needs to use fat arrow again

Adding remove
--

- Add a destroy button to the note div
- Add `position: relative` to `.note`
- Button is positioned absolutely, shows on hover, top 7 right 5
- tabindex="-1"

- Add the destroy route/action to rails
- Bind the click event to call `@model.destroy()`
- Bind to the model's destroy event in the constructor and call @remove
- Bind to the model's destroy event in `NotesIndex` and `@stopListening(view)`
  - Console.log the view before and after to demonstrate the events sticking
    around

Adding some validation
--

- We've inadvertently made it possible to save empty notes.
- Title or body can be blank, not both
- Add method `validate` to the model
  - Return something if the title and content are both blank

- Bind to invalid in `ViewsNote` and add an invalid class
  - `border-color: red`
  - Remove in `beginEditing`

- Move validation to model
- Note that invalid is not triggered
  - Demonstrate that we can trigger it with the right error by overloading sync
  - Demonstrate that we can bind to the 'error' event instead, and get the
    returned arguments from that
  - Move validation back to the JS model

- Commit!
  - end adding-and-removing
