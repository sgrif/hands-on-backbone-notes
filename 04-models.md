Backbone Models
==

- While it's cool that we don't have to persist anything for the pages to
  update, we probably want to be able to refresh the page without losing all of
  our data
- Backbone will do most of the heavy lifting for us, but first we need to change
  our plain old Javascript classes to Models
- Wrap each line of AllNotes with new Backbone.Model
  - Models in Backbone don't care about your schema, you can set any attribute
    on any model. Because of this, we don't even need to create a separate class
    if we only want a dumb data container
  - ```
    window.ScratchPad =
      Models: {}
      Collections: {}
      Views: {}
      Routers: {}
      initialize: ->
        @AllNotes = [
          new Backbone.Model(id: 1, title: 'make a second todo item', complete: true),
          new Backbone.Model(id: 2, title: 'Make another todo list', complete: false),
          new Backbone.Model(id: 3, title: 'DSICO PARTY!', complete: false)
        ]
        new @Routers.ScratchPadRouter
        Backbone.history.start(pushState: true)
    ```
- Note that our views have all broken
  - Change getters in our index.jst.eco template to use the .get method
    - Note that we can leave id alone
    - We could also have passed the plain old Javascript object to the view, accessed things via
      `@title`, and only had to change the render method to
      `@template(@model.attributes)`
      - It's a style preference
      - Some people prefer to do `@template(@model.toJSON())` instead of
        attributes. I do not ever use `toJSON` in this fashion, so I can reserve
        it for translating between my client side and server side domain, more on
        that later.
  - ```
    <ul>
      <% for note in @notes: %>
        <li>
          <dl>
            <dt>Title</dt>
            <dd><a href="/notes/<%= note.id  %>"><%= note.get('title') %></a></dd>
            <dt>Content</dt>
            <dd><%= note.get('content') %></dd>
          </dl>
        </li>
      <% end %>
    </ul>
    ```
  - We need to make the same changes to our edit.jst.eco template
  - ```
    <input type="test" class="note-title" value="<%= @note.get('title') %>" />
    <br />
    <textarea class="note-content"><%= @note.get('content') %></textaream>
    <br />
    <input type="submit" />
    ```
  - Change setters in our EditNote Backbone view to use the .set method
    - We have to make this change for the same reason we use get above
    - ```
      saveModel: (e) ->
        @model.set
          title: @$('.title').val()
          content: @$('.content').val()
        Backbone.history.navigate('/', trigger: true)
        false
      ```

Removing the hard coded edit link
--

- Part of a model's responsibility is knowing the it's URL for persistence. That
  also happens to be the URL we're using for display. Let's refactor to take
  advantage of this.
- Change href to call `note.url()`
- ```
  <ul>
    <% for note in @notes: %>
      <li>
        <dl>
          <dt>Title</dt>
          <dd><a href="<%= note.url() %>"><%= note.get('title') %></a></dd>
          <dt>Content</dt>
          <dd><%= note.get('content') %></dd>
        </dl>
      </li>
    <% end %>
  </ul>
  ```
- To do this, we must create a `Note` model.
- ```
  class App.Models.Note extend Backbone.Model
    urlRoot: '/notes'
  ````
- Finally we'll need to update our ScratchPad app
- ```
  window.ScratchPad =
    Models: {}
    Collections: {}
    Views: {}
    Routers: {}
    initialize: ->
      @AllNotes = [
        new @Models.Note(id: 1, title: 'make a second todo item', complete: true),
        new @Models.Note(id: 2, title: 'Make another todo list', complete: false),
        new @Models.Note(id: 3, title: 'DSICO PARTY!', complete: false)
      ]
      new @Routers.ScratchPadRouter
      Backbone.history.start(pushState: true)
  ```

- Commit!
  - end backbone-models
