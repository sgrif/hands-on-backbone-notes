Backbone.js Collections and Connecting to the Rails API
--

- If we didn't have Backbone in order to retreive our list of notes we would
  have to use some code similar to the following
  - ```
    var notes
    notesPromise = $.ajax('/notes.json')
    noteFactory = function(data) { return new App.Models.Note(data) }
    notesPromise.done(function(data) { notes = _.map(data, noteFactory) })
    notes
    ```

Luckily, Backbone again provides some plumbing to do the heavy lifting of this
for us in the form of Collections.

- Create a `Collections.Notes` class, with a `url` and `model` property
  - ```
    class App.Collections.Notes extends Backbone.Collection
      url: '/notes'
      model: App.Models.Note
    ```
  - After we've created the collection and associated with our Note Backbone
    model we can remove the url setting from our model
    - Every model knows that it's a part of a collection, and we can access it via
      the collection property.
- Now that we've created our Notes collection let's use it our application
  initializer
  - Wrap the rest of `initialize` in a success callback to fetch since the
    collection is loading asynchronously
  - ```
    window.ScratchPad =
      Models: {}
      Collections: {}
      Views: {}
      Routers: {}
      initialize: ->
        @AllNotes = new @Collections.Notes
        @AllNotes.fetch().done =>
          new @Routers.ScratchPadRouter
          Backbone.history.start(pushState: true)
    ```
- Finally we need to change loop in our index.jst.eco template to `@notes.models`
  - ```
    <ul>
      <% for note in @notes.models: %>
        <li>
          <dl>
            <dt>Title</dt>
            <dd><a href="/notes/<%= note.id  %>"><%= note.get('title') %></a></dd>
            <dt>Content</dt>
            <dd><%= note.get('content') %></dd>
        </li>
      <% end %>
    </ul>
    ```
- Edit is now broken, as well, since we don't access collections as arrays.
  - In our router's showNote method we need to make a couple of adjustments
  - We don't need to do `id - 1`, since `get` takes an id, not an index
  - Change the router to call `.get` on the AllNotes collection instead of []
  - ```
    class App.Routers.ScratchPadRouter extends Backbone.Router
      routes:
        '': -> 'index'
        '/notes/:id': 'showNote'

      index: ->
        view = new App.Views.Notes(collection: App.AllNotes)
        $('#container').html(view.render().el)

      showNote: (id) ->
        model = App.AllNotes.get(id)
        view = new App.Views.EditNote(model: model)
        $('#container').html(view.render().el)
    ```
- Check everything is still working
- Commit!

Actually persisting
--

Now for the magic. All we need to do in order to make our views save is change
the call to `set` to `save`, passing in the same arguments in our EditNote
Backbone view's saveModel method.
- ```
  saveModel: (e) ->
    @model.save
      title: @$('.title').val()
      content: @$('.content').val()
    Backbone.history.navigate('/', trigger: true)
    false
  ```
- Commit!
