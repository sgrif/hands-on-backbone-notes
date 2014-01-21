Validations
--

- We've inadvertently made it possible to save empty notes.
- Title or body can be blank, not both
- Add method `validate` to the model
  - Return something if the title and content are both blank
  - This only prevents saving if set and save are called separately

- Bind to invalid in `Views.Note` and add an invalid class
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
