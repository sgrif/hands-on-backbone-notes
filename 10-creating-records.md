Creating Records
==

We've built a great bit of functionality for editing, but we've forgotten to
give the user the ability to add new items, and delete them once they're there.

Adding add
--

- Let's start by adding an add button to the page
  - Add a div with the class `add-actions` to the bottom of our index.jst.eco
    template
  - Add a paragraph tag inside of the div with a button inside of it with a
    class of `add-note`
  - Make the button say 'Add Note'
  - ```
    <div class="add-actions">
      <button class="add-note">Add Note</button>
    </div>
    ```
- Open the notes.css.scss file
  - Add styles for add-actions
  - ```
    .note, .add-actions {
      width: 225px;
      heigth: 225px;
      margin: 20px;
      float: left;
      padding: 10px;

      position: relative;
    }
    ```
  - Add styles for the p tag inside of the add-actions div to place the button
    where we want it
  - ```
    .add-actions {
      p {
        position: absolute;
        top: 50%;
        height: 3em;
        margin-top: -1.5em;
      }
    }
    ```
- Open the index.jst.eco template
  - Remvoe the add-actions div
- Create a new template called add\_ations.jst.eco
  - Add the p tag you removed from the index.jst.eco file into this new template
  - ```
    <p>
      <button class="add-note">Add Note</button>
    </p>
    ```
- In the Notes Backbone view add an initialize method
  - In the initialize method initialize an instance of the AddActions view
  - In the render method append this addActions view to the object el
  - ```
    class App.Views.Notes extends Backbone.View
      template: JST['notes/index']

      initialize: ->
        @addActions = new App.Views.AddActions(collection: @collection)

      render: =>
        @$el.html(@template())
        @collection.forEach(@renderNote)
        @$el.append(@addActions.render().el)
        this

      renderNote: (note) =>
        view = new App.Views.ShowNote(model: note, tagName: 'li')
        @$('.notes').append(view.render().el)
    ```
- Now we need to create the `AddActions` view we created an instance of in the
  previous step
  - Connect the view to the add\_actions.jst.eco template
  - Create the render method
  - Bind the click event to a method called `addNote`
    - In the `addNote` method call the add method on the `@collection` object
    - Make sure to send in an empy opject to the add method (must pass an empty object to the add method or it's noop)
  - ```
    class App.Views.AddActions extends Backbone.View
      template: JST['notes/add-actions']

      className: 'add-actions'

      events:
        'click .add-note': 'addNote'

      render: ->
        @$el.html(@template())
        this

      addNote: ->
        @collection.add({})
        false
    ```

Rendering the new Note
--

- In the `Notes` view's initialize method we add to listen to our `@collection`
  for an event called `add` using Backbone's `listenTo` method
  - Set the render method as the `add` events handler
  - Change the render method to use the fat arrow
  - ```
    initialize: ->
        @addActions = new App.Views.AddActions(collection: @collection)
        @listenTo(@collection, 'add', @renderNote)
    ```
- In the show.jst.eco templateadd placeholders to the inputs to enhance UX

- Currently we're re-rendering the entire page anytime a note is added we can
  avoid this by only rendering the newly added note
  - Because we already have a method to render a single note in our `Notes` view
    we can use that as our call back to the `add` event and avoid the hastle of
    rerendinering the entire list of Notes
  - Remeber to change renderNote to use fat arrow

- Add an additional event handler on our `@collection` object in the `Notes`
  initializer to handle the `reset` event
  - This event handler will call the `Notes` render method
  - ```
    initialize: ->
        @addActions = new App.Views.AddActions(collection: @collection)
        @listenTo(@collection, 'reset', @render)
        @listenTo(@collection, 'add', @renderNote)
    ```
