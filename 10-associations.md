Associations
==

In preparation for adding todo lists, let's separate the common element between
todos and notes (the title) from the piece that's different (the content)

Setting up the JSON
--

- NOTE: `ScratchPadItem` is a shit name, if you think of something better, use
  it
- `rails g model ScratchPadItem title:string body:references`
  - body is polymorphic
  - Create record for each note with the appropriate association and title set
- Add content method to `ScratchPadItem` which delegates to `body`
- Create controller with index method
  - `mkdir -p app/views/scratch_pad_items`
  - `mv app/views/notes/index.html.erb app/views/scratch_pad_items/`
  - Change routes
  - Update html view
    - Rename `AllNotes` to `AllItems`
      - Don't change collection class
    - `.to_json(methods: :content)`
  - Check things are working

- Update seeds

Handling the association
--

- I'd still like to just have a `Note` model in the JS side, but we need to
  handle the new API structure
- Change the JSON to include `body`
- Override the parse method in the model
- Maintain the same data structure we had before
- Need to pass parse: true to the constructor of the collection

- Commit!
  - end rendering-association

Making it save
--

- Even though everything is rendering properly, saving is broken.
- First, let's get it saving to the correct url, and send the data to the server
  in the format we got it.
- Rename collection to `ScratchPadItems`
- Set the URL to `/scratch_pad_items`
- Define `toJSON` to return the proper structure
- Make sure we're calling the right URL with the right parameters

- We can use `accepts_nested_attributes_for`, but a few modifications are
  required
  - Don't forget `update_only: true`
  - Add a presence validator to `body` as a sanity check
- Add routes
- Create a form object that takes a model and parameters, and create it in the
  update action
- define a `permitted_params` method in the form object (assume always `Note`
  for the time being)
- Create a `save!` method

- Timestamp isn't updating if we only change the content. Unfortunately,
  `has_one` doesn't take `touch` as an option.
  - Add an `after_save` hook on `Note`
  - Add an `inverse_of` option to the `belongs_to` in `ScratchPadItem`

- Check that update is now correctly saving the title and body, and updating the
  timestamp
  - Note that we may need to restart the server since we've added a new folder
    to /app
  - Yay Rails 4 for automatically loading all folders in app

- Commit!
  - end updating-association

Creating and Destroying
--

- Add a create method to the controller, extract update to a `save_form_for`
  method.
- We get an error saying we can't build a one-to-one polymorphic association
- Add `scratch_pad_item.body ||= Note.new` to the beginning of our `save!`
- Add a destroy method to the controller
- Add `dependent :destroy` to the `belongs_to`
- Check that destroy is working
  method
- Check that create is working with just a title, just a body, and both

- Commit!
  - end associations
