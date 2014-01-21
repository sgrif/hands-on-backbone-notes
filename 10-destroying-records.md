Destroying Records
==

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
