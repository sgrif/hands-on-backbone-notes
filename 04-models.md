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
    - We could also have passed the POJO to the view, accessed things via
      `@title`, and only had to change the render method to
      `@template(@model.attributes)`
      - It's a style preference
      - Some people prefer to do `@template(@model.toJSON())` instead of
        attributes. I do not ever use `toJSON` in this fashion, so I can reserve
        it for translating between my client side and server side domain, more on
        that later.
  - Change setters in view to .set
    - Demonstrate both `@model.set('title', @$('.title').val())` and
      `@model.set(title: @$('.title').val())`

Removing the hard coded edit link
--

- Part of a model's responsibility is knowing the it's URL for persistence. That
  also happens to be the URL we're using for display. Let's refactor to take
  advantage of this.
- Change href to call `note.url()`
  - To do this, we must create a `Note` model.

- Commit!
  - end backbone-models
