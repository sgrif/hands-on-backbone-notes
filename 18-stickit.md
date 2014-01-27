Stickit: Backbone.js Databinding Plugin
==

Setting up stickit
--

- Add the gem `backbone-stickit-rails` to the Gemfile
- `bundle` and then restart the server
- In the `application.js` file include backbone.stickit after backbone

Using stickit
--
- Now let's see it in action
- Open the ShowNote Backbone view
  - Add a bindings hash to connect classes with attributes on the model
  - ```
    bindings:
      '.note-title': 'title'
      '.note-content': 'content'
    ```
  - Now we can remove the set logic from the `save` method
  - ```
    save: ->
      @model.save()
      false
    ```
  - We need to add a call to the new method `stickit` in the `render` method
    this will bind the models attribute values to the classes in our template
  - ```
    render: ->
      @$el.html(@template(note: @model))
      @lastUpdated.setElement(@$('.normal-footer')).render()
      @noteBody.setElement(@$('.body')).render()
      @stickit()
      this
    ```

Fixing Todo Item Save
--
- We accidentally broke save on todo items at the end of the last lesson let's
  take a second to fix it
- Open the ShowNote Backbone view
  - Remove the 'change .note-content': 'saveModel'
- Open the StickyNote Backbone view
  - Add an events has
  - ```
    events:
      'change .note-content': 'saveModel'
    ```
  - Add a `saveModel` method to the class
  - ```
    saveModel: ->
      @model.save()
      false
    ```
- Open the TodoList Backbone model
  - We need to change the `toJSON` method to filter on `isValid` instead of `isNew`
  - We need to switch from the `reject` method to the `filter` method
  - ```
    toJSON: ->
      {
        title: @get('title')
        body:
          type: 'todo_list'
          todo_list:
            todo_items: @todoItems.filter((i) -> i.isValid())
      }

Stickit and Templates
--

- Another nice feature of stickit is we can now remove all dynamic content fromm
  our templates
- Open the notes show.jst.eco template
  - Remove all dynamic content
  - ```
    <header class="title-container">
      <input type="text" class="note-title" placeholder="Title..." />
    </header>
    <div class="body">
    </div>
    <div class="normal-footer"></div>
    <div class="editing-footer">
      <button class="save">Save</button>
    </div>
    <button class="destroy-note" tabindex="-1">Remove</button>
    ```
- Open the notes stick_notes.jst.eco file
  - Remove all dynamic content
  - ```
    <textarea class="note-content" placeholder="Content..."></textarea>
    ```
- Open the todo_item show.jst.eco file
  - Remove all dynamic content
  - ```
    <input type="checkbox" class="todo-item-complete" />
    <input type="text" class="todo-item-title" placeholder="Add a new todo item..." />
    ```
- To make the todo item binding change we just made work we need to add bindings
  to the TodoItem Backbone view
  - ```
    bindings:
      '.todo-item-title': 'title'
    ```
  - Add a call to the `stickit` method in the `render` method
  - ```
    render: =>
      @$el.html(@template(todoItem: @model))
      @renderComplete()
      @stickit()
      this
    ```
  - Remove the title from the `set` method call in `saveModal`
  - We have to leave complete because stickit doesn't work with checkboxes
  - ```
    saveModel: ->
      @model.set
        complete: @$('.todo-item-complete').is(':checked')

      if @model.hasTitle()
        @model.save()
      else
        @model.destroy()
        @remove()
      false
    ```
