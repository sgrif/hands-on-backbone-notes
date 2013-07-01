Refactoring
==

We've got a few things we're doing that feel wrong. Let's see if we can clean
things up.

Bootstrapping AllNotes
--

- We know we always need `AllNotes`. Why should it be a separate request?
- The term is called 'bootstrapping'
- Change root to a new controller called Main
- Set @notes in index
- Add some in-line JavaScript to main#index, and set AllNotes there.
- Delete the loading code from initialize

Rendering collections with sub-views
--

- Looping through the models property in the index view also feels weird
- Rename `Notes` to `NotesIndex`
- Create a `Note` view
- Add an id to the `ul` tag
- Pull out the `li` segment of the index template to notes/show
- Remove the `li` tag, and set `tagName` to `li` on `Views.Note`
  - Set `className` to `note` as well
  - Mention that we could set an `id` property as well, and that these can be
    set to functions to evaluate at runtime, or passed to the constructor.
- `%s/note/@note/g`
- Move `events` and `show` on `NotesIndex` to `Note`
- Remove the argument to `template` in `render`
- Change render in `NotesIndex` to loop through the collection and append a new
  `Note` view.
- Check that everything still works

Decoupling logic from the DOM
--

It seems silly to perform some logic to set an href on an anchor tag, use some
JavaScript to prevent the link from redirecting to that href, and using more
JavaScript to then grab that href. Let's simplify.

- Change the href to '#' (still need something or the browser won't style it)
- Add a class of `edit` to the link
- Change the selector to `.edit`
- Change the `edit` method to just navigate to `@model.url()`

- Commit!
  - end refactoring
