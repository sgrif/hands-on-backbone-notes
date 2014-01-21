Converting to a Single Page Application
==

Since we've gone through all this trouble to never actually leave the page, why
are we going through even more trouble to make it look like we are?

Styling
--

First let's make things look more appropriate.

- 225x225, 20px margin, float left
- 1px black border
- 10px padding

- Remove dl and replace with input wrapped in header tag
- height + line-height of 25 on div
- 10px bottom margin, black border for separation
- input is bold 17px font, no border no background
  - no outline on :focus for all inputs

- wrap content in a body div and textarea
- 165px tall, resize: none

Wiring up
--

- Create a save method, which grabs the title and body from the inputs
- Bind a change event with no target to that method
  - Mention that if you don't specify a target, the event gets bound to @$el,
    and events like change or click bubble up from children
- Title is single line so it should save and blur when enter is pressed
- Bind the keypress event to call save if e.keyCode == 13
- Create an endEditing method, and call it at the end of save
  - `@$(':input').blur()`

Improving the UX
--

It may not immediately be apparent how to save changes, so let's make it more
obvious.

- Add an editing class on `focus :input`
- Remove it on `blur :input`
- Add a footer with a save button
  - `display: none`
  - `display: block` when `.editing`
- Add a blue border to the editing class
- `'click .save': 'save'`

Clean Up
--

We can remove the following:

- The router
- The wildcard route in rails
- The Edit view and associated template

We then need to change the initialize method to render the `Notes` view
