Improving our Backbone.js Application
==

We've got a few things we're doing that feel wrong. Let's see if we can clean
things up.

Bootstrapping AllNotes
--

- We know we always need `AllNotes`. Why should it be a separate request?
- The term is called 'bootstrapping'
  - Change root to 'notes#index' and in the new index page set
    ScratchPad.AllNotes
  - Create the new notes index.html.erb file
  - ```
    <script>
      ScratchPad.AllNotes = new ScratchPad.Collections.Notes(<%= raw notes.to_json %>)
    </script>
    ```
    - Note: We don't need to worry about XSS here, `to\_json` properly escapes
      strings
  - Delete the loading code from the ScratchPad initialize method
  - ```
    window.ScratchPad =
      Models: {}
      Collections: {}
      Views: {}
      Routers: {}
      initialize: ->
        new @Routers.ScratchPadRouter
        Backbone.history.start(pushState: true)
    ```
- While this approach is better, we can decouple further
  - Modify the notes index.html.erb file to set a notesJson attribute on
    ScratchPad
  - ```
    <script>
      ScratchPad.notesJson = <%= raw notes.to_json %>
    </script>
    ```
  - And in the ScratchPad initalize we can set AllNotes based on the JSON
  - ```
    window.ScratchPad =
      Models: {}
      Collections: {}
      Views: {}
      Routers: {}
      initialize: ->
        @AllNotes = new @Collections.Notes(@notesJson)
        new @Routers.ScratchPadRouter
        Backbone.history.start(pushState: true)
    ```

Rendering collections with sub-views
--

- Looping through the models property in the note index.jst.eco file should be
  avoided when possible
  - Remove everything from the index.jst.eco file except for the 'ul'
  - Add a class of "notes" to the ul in notes index.jst.eco
  - ```
    <ul class="notes>
    </ul>
    ```
  - Create a notes show.jst.eco template
  - Pull out the `li` segment of the notes index.jst.eco template and put it in the to show.jst.eco template leaving the 'li' tag out
  - In the show.jst.eco template change `note` to `@note`
  - ```
    <dl>
      <dt>Title</dt>
      <dd><a href="<%= @note.url() %>"><%= @note.get('title') %></a></dd>
      <dt>Content</dt>
      <dd><%= @note.get('content') %></dd>
    </dl>
    ```
  - Now we need to edit our Notes Backbone view
    - We'll iterate over the view's collection attribute (instead of passing it
      to the template) and render a new single note view we'll create below
    - Notice our new renderNote method requires a fat arrow
    - ```
      class App.Views.Notes extends backbone.view
        template: jst['notes/index']

        events:
          'click a': 'showNote'

        render: ->
          @$el.html(@template(notes: @collection))
          @collection.forEach(@renderNote)
          this

        showNote: (e) ->
          $this = $(e.currenttarget)
          backbone.history.navigate($this.attr('href'))
          false

        renderNote: (e) =>
          view = new App.Views.ShowNote(model: note)
          @$('.notes').append(view.render().el)
      ```
  - Create the new view from above called ShowNote
  - Set the `className` property on the Note view to `note`
  - ```
    class App.Views.ShowNote extends Backbone.View
      template: JST['notes/show']

      className: 'note'

      render: ->
        @$el.html(@template(note: @model))
    ```
  - Change the Notes Backbone view's renderNote method to pass in the tag we
    want to wrap the Note template in
  - ```
    renderNote: (e) =>
      view = new App.Views.ShowNote(model: note, tagName: 'li')
      @$('.notes').append(view.render().el)
    ```
  - Move `events` and `showNote` from the `Notes` view to the `Note` view
  - ```
    class App.Views.ShowNote extends Backbone.View
      template: JST['notes/show']

      events:
        'click a': 'showNote'

      className: 'note'

      render: ->
        @$el.html(@template(note: @model))

      showNote: ->
        Backbone.history.navigate(@model.url(), trigger: true)
        false
    ```
  - We can change our index.jst.eco template's link tag to link to '#'
    instead of the actual url
  - We also want to add a class to our anchor tag so we can avoid directly
    coupling to the dom
  - ```
    <dd><a href="#" class="edit-note"><%= @note.get('title') %></a></dd>
    ```
  - Now we can edit our ShowNote Backbone view's events hash to the following
  - ```
    events:
      'click .edit-note': 'showNote'
    ```
