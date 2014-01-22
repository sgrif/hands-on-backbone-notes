Backbone.js Views and Templates
--

- Add an array called `AllNotes` to the `ScratchPad` initialize method
  - Populate the AllNotes array the values from our seed file and add an id
    attribute
  - ```
    window.ScratchPad =
      Models: {}
      Collections: {}
      Views: {}
      Routers: {}
      initialize: ->
        @AllNotes = [
          { id: 1, title: 'make a second todo item', complete: true },
          { id: 2, title: 'Make another todo list', complete: false },
          { id: 3, title: 'DSICO PARTY!', complete: false }
        ]
        new @Routers.ScratchPadRouter
        Backbone.history.start(pushState: true)
    ```
- Create an empty Notes Backbone view at
  `app/assets/javascripts/views/notes.js.coffe`
- ```
  class App.Views.Notes extends Backbone.view
  ```
- In order to make our view work we'll modify the index route in our Backbone
  router
  - Route the index to an instance of our view and pass the AllNotes variable to
    it
  - We pass the AllNotes variable to our view in an object with the key of
    collection
  - After creating our instance of the Backbone view we need to render it and
    appened it to a DOM element so it's visible we'll use a container div
  - ```
    class App.Routers.ScratchPadRouter extends Backbone.Router
      routes:
        '': -> 'index'
        '/notes/:id': 'showNote'

      index: ->
        view = new App.Views.Notes(collection: App.AllNotes)
        $('#container').html(view.render().el)

      showNote: (id) ->
        alert("You requested the note with the id of #{id}")
    ```
- Next we need to add a render method to our Notes Backbone view
  - ```
    class App.Views.Notes extends Backbone.View
      render: ->
        @$el.html('Hello from the Notes view')
        this
    ```
- Now add a div to the application.html.erb file in the layouts folder with the id of 'container'
- Backbone template options
  - Add a template method to the Notes Backbone view file
  - One option that actually comes with backbone itself is `_.template`. It works
    a lot like ERB, however you call it like
    `_.template('<h1><%= title %></h1>')(title: 'Hello World')`
  - We're going to use something called 'eco', which the backbone-on-rails gem
    includes for us. ERB stands for Embedded Ruby. ECO is Embedded CoffeeScript,
    and the syntax will feel very familiar
  - Set the new template method on the Notes Backbone view to the value returned from a
    hash called JST when it recieved the path to the view as a key `template: JST['notes/index']`
  - ```
    class App.Views.Notes extends Backbone.View
      template: JST['notes/index']

      render: ->
        @$el.html('Hello from the Notes view')
        this
    ```
- Create a file named app/assets/templates/notes/index.jst.eco
  - Copy the contents of app/views/notes/index.html.erb into our new jst view
  - Change `@notes.each` to `for note in @notes:`
    - The colon at the end of the line is required because jst templates don't
      lend themselves to relevant whitespace, so we we use a colon
  - Replace the call to link\_to with a hard coded anchor tag
  - Add a render method with the following code in the body `@$el.html(@template(notes: @collection)); this`
    - The view's constructor accepts two arguments by default @collection and @model which we will discuss in greater detail later
  - ```
    <ul>
      <% for note in @notes: %>
        <li>
          <dl>
            <dt>Title</dt>
            <dd><a href="/notes/<%= note.id  %>"><%= note.title %></a></dd>
            <dt>Content</dt>
            <dd><%= note.content %></dd>
        </li>
      <% end %>
    </ul>
    ```
- Now we need to revisit the Notes Backbone view and pass in the notes we want
  our template to render
  - Notice we use the collection attribute
  - ```
    class App.Views.Notes extends Backbone.View
      template: JST['notes/index']

      render: ->
        @$el.html(@template(notes: @collection))
        this
    ```
- If we refresh our browser we see that our view is working, but the link is not
  yet async
- In order to make the link function asynchronously we need to do the following
  - Add an events hash to our Notes view
  - Define an event on click by adding of an anchor tag in the view. This is
    achieved by adding a value to the hash table whose key is 'click a' and
    value is either a function or a method name in the view
  - Your event Notes Backbone view should look like this
  - ```
    class App.Views.Notes extends backbone.view
      template: jst['notes/index']

      events:
        'click a': 'showNote'

      render: ->
        @$el.html(@template(notes: @collection))
        this

      showNote: (e) ->
        $this = $(e.currenttarget)
        backbone.history.navigate($this.attr('href'))
        false
    ```
- If we go back to the browser and click the title link you'll notice the page
  doesn't change, but the URL is modified
  - This is caused by Backbone defaults. In order to make backbone attempt to
    route the url you have to pass trigger: true to the navigate method
  - ```
    showNote: (e) ->
      $this = $(e.currentTarget)
      Backbone.history.navigate($this.attr('href'), trigger: true)
      false
    ```
- If you push the back and forward buttons you'll notice history still works as
  well
  - Backbone doesn't require you to write a bunch of boilerplate for `pushState` and `popState`

- Now we will create a view for a single Note
  - Modify the router so the showNote method creates an instance of the new
    view with a @model from the AllNotes collection based on the id passed in
    the url
  - ```
    class App.Routers.ScratchPadRouter extends Backbone.Router
      routes:
        '': -> 'index'
        '/notes/:id': 'showNote'

      index: ->
        view = new App.Views.Notes(collection: App.AllNotes)
        $('#container').html(view.render().el)

      showNote: (id) ->
        model = App.AllNotes[id - 1]
        view = new App.Views.EditNote(model: model)
        $('#container').html(view.render().el)
    ```
  - There's not really any reason to have a 'show' action for a single note, so
    we're going to combine show and edit
  - In the EditNote view change the tag that the view will render from a div to
    a form with the following `tagName: 'form'`
  - ```
    class App.Views.EditNote extends backbone.view
      template: jst['notes/edit']

      tagName: 'form'

      render: ->
        @$el.html(@template(note: @model))
        this
    ```
  - Add a new template at app/assets/template/edit.jst.eco and build out a
    simple form
  - ```
    <input type="test" class="note-title" value="<%= @note.title %>" />
    <br />
    <textarea class="note-content"><%= @note.content %></textaream>
    <br />
    <input type="submit" />
    ```
  - Add 'submit' to the events hash
    - In the submit event handler pull the values out of the form manually
    - ```
      class App.Views.EditNote extends backbone.view
        template: jst['notes/edit']

        tagName: 'form'

        events:
          'submit': 'saveModel'

        render: ->
          @$el.html(@template(note: @model))
          this

        saveModel: (e) ->
          @model.set
            title: @$('.title').val()
            content: @$('.content').val()
          Backbone.history.navigate('/', trigger: true)
          false
      ```

  - In it's current state the values in the browser will update, but the values
    are not being persisted to the database.
- Commit!
