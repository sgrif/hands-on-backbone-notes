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
  - `mv app/views/main/index.html.erb app/views/scratch_pad_items/`
  - Delete main controller
  - Change routes
  - Update html view
    - Rename `AllNotes` to `AllItems`
    - `.to_json(methods: :content)`
  - Check things are working

- Go over the options that `to_json` takes, and give ridiculous example of how
  complex it can be
- Demonstrate both forms:
  - `json.title scratch_pad_item.title`
  - `json.extract! scratch_pad_item, :id, :title, :content, :created_at, :updated_at`
- `collection = new Backbone.Collection([], {url: '/scatch_pad_items'});
  collection.fetch()` in the console to test
- We need to use this view in our html view
  - `mv index.json.jbuilder _scratch_pad_items.json.jbuilder`
  - index.json: `json.partial! 'scratch_pad_items', scratch_pad_items: @scratch_pad_items`
  - index.html: `render('scratch_pad_items.json', scratch_pad_items:
    @scratch_pad_items).html_safe`
  - Could be extracted to helper method:
    - `
      def render_json(collection)
        table_name = collection.table_name
        render(table_name, table_name => collection).html_safe
      end
      `
  - Both are ugly, hopefully Rails will give us a better way to do this soon.
- Check things are still working
- Update seeds

Rendering associations in Backbone
--

- Let's handle the association in the JS
- Change view to include body under the body key
- Create skeleton collection and model class
- Load collection in the console and demonstrate that body is just a POJO
- `@body = new ScratchPad.Models.Note(@get('body')); @unset('body')` in constuctor
  - Break that into multiple lines
- Demonstrate that the association now works
  - Call `save` and look at what's sent to the server
  - Note that the Note is serialized properly
- Change collection class in index.html

- Rendering is still working, but the content is blank
- Index is improperly named, change the name of the class
- Index template is named wrong. Rename it. Change the id of the div
- Check things are still working after each rename
  - Mention that normally we'd write unit tests to make sure we don't break
    things when doing things like renaming
- `Note` view is named wrong, rename it
- Show template is named wrong, rename the file
- Change `@note` to `@scratch_pad_item` or `@model` to be concise
- Change `className` in view
- Rename stylesheet file, and change class
- Make sure everything is still working and styled after all renames

- In show template, replace `textarea` with `div` with same class
- Create notes/show and paste `textarea`, renaming class to content
- Create empty `Note` view with correct template
- Create note subview in constructor, and `setElement` in render
  - Now is a good time to monkey patch the `assign` method
  - Note the syntax in CoffeeScript
- Styles on the textarea are broken
- Move the `.body` styles to a notes stylesheet, change the selector to
  `.content`, and wrap it in `.note`
- Styles are still broken
  - Add `className` to `Note` view
  - Note this doesn't work with `setElement`
  - Replace with `@$el.addClass` in `render`

- Commit!
  - end rendering-association

Making it save
--

- Our views aren't setting the attributes properly.
- Remove the `save` method, and call `@model.save()` with no args instead
- In both views, bind to the change event on the specific input required
  - This can get tedious in large applications. Recommend looking into
    Backbone.StickIt (or Marionette, which already includes it)

- Even though everything is rendering properly, saving is broken.
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
- Delegate `to_json` to the model, and return that in the controller (to
  properly update `updated_at`)
  - Note that this won't tell Backbone the updated timestamps on associations.
    We could write some more complex code to build the association if that
    behavior was needed

- On the backbone model, override `toJSON` and set `body_attributes`
  - Mention the irony that `toJSON` isn't expected to return a JSON string

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

Fixing Validations
--

- Our validations have broken as well, and the system is happily letting us save
  empty notes
- Since how we perform validation will depend on the type of body, we'll leave
  the validation where it is.
- Define `validate` on `ScratchPadItem` and return `@body.validate()`
- This fails because the body doesn't have a title, any longer. We need the body
  to know about the parent.
- Set the `scratch_pad_item` property of `body` in the constructor, and update
  `Note#validate` appropriately
- This still fails, because on a new model, the properties are undefined.
  - Demonstrate that we could solve the problem by setting a defaults array with
    ''
  - A better solution would be to use the existential operator
    - The existential operator can be used to check for null and undefined, and
      will also absorb nulls in method chains
    - `unless @scratch_pad_item.get('title')?.trim() or @get('content')?.trim()`
    - In JS, empty strings are falsey
- Check that everything is working as expected

- Commit!
  - end associations
