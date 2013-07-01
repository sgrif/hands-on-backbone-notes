Backbone Models
==

- While it's cool that we don't have to persist anything for the pages to
  update, we probably want to be able to refresh the page without losing all of
  our data
- Backbone will do most of the heavy lifting for us, but first we need to change
  our POJOs to Models
- Wrap each line of AllNotes with new Backbone.Model
  - Models in Backbone don't care about your schema, you can set any attribute
    on any model. Because of this, we don't even need to create a separate class
    if we only want a dumb data container
- Note that our views have all broken
  - Change getters in template to .get
    - Note that we can leave id alone
  - Change setters in view to .set
    - Demonstrate both `@model.set('title', @$('.title').val())` and
      `@model.set(title: @$('.title').val())`

Removing the hard coded edit link
--

- Part of a model's responsibility is knowing the it's URL for persistence. That
  also happens to be the URL we're using for display. Let's refactor to take
  advantage of this.
- Change href to call `note.url()`
  - Note this doesn't work without passing urlRoot to the constructor.
  - Remove the duplication by creating a Note model
    - We can remove the curly braces when we do this
    - We also need to move setting ScratchPad.AllNotes into $(document).ready so
      the class is loaded

- Commit!
  - end backbone-models
