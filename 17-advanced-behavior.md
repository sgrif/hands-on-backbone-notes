Advanced Backbone.js Behavior
==

Adding new todos
--

- The easiest way we can get a simple interface is just to always render a todo
  view with a new item on the bottom
- Open the TodList Backbone view
  - Listen to the todoItems reset event in the initializer
  - ```
    initialize: ->
      @todoItems = new App.Views.TodoItemsIndex(collection: @model.todoItems)
      @listenTo(@model.todoItems, "reset", @addNewUnlessExists)
    ```
  - The event handler will be called `addNewUnlessExists`
  - ```
    addNewUnlessExists: =>
      @model.todoItems.add({}) unless @model.todoItems.last().isNew()
    ```
  - We will also need to add a call to the `addNewUnlessExists` method to the
    objects `render` method
  - ```
    render: ->
      @$el.html(@todoItems.render().el)
      @addNewUnlessExists()
      this
    ```
- Add a placeholder to the todo item show.jst.eco template text input
  - ```
    <input type="text" class="todo-item-title" value="<%= @todoItem.get('title') %>" placeholder="Add a new todo item..." />
    ```
- Now that we have our new todo item text box rendering we want to disable the
  completed checkbox for that input
- In the TodoItem Backbone view
  - Modify the `renderComplete` method to disable the checkbox for new items
  - ```
    renderComplete: ->
      @$('.todo-item-title').attr('disabled', @model.get('complete'))
      @$('.todo-item-complete').attr('checked', @model.get('complete'))
        .attr('disabled', @model.isNew())
    ```
- Next we need to add a validation to the TodoItem Backbone model to ensure that
  a todo item has a title
  - In the TodoItem Backbone model add a validation method
  - ```
    validation: ->
      unless @has('title') && @get('title').trim() != ""
        return "Must provide a title"
    ```
  - Because our save is overriden we need to manually call validate as well
  - ```
    save: ->
      unless @validation()
        @collection.todoList.save()
    ```
  - To make our has title logic easily available to other objects let's extract
    a helper method and use that in our validation
  - ```
    hasTitle: ->
      @has('title') && @get('title').trim() != ""
    ```
  - ```
    validation: ->
      unless @hasTitle()
        return "Must provide a title"
    ```
- We want to make todo items destroy them selves if we delete the title from the
  UI
- This will require some additional work in the Rails app, but let's start with
  the client side
- In the TodoItem Backbone view we need to modify the `saveModel` method
  - Add a conditional that checks if a todo item has a title
  - If it does save it
  - If not destroy it
  - ```
    saveModel: ->
      @model.set
        title: @$('.todo-item-title').val()
        complete: @$('.todo-item-complete').is(':checked')

      if @model.hasTitle()
        @model.save()
      else
        @model.destroy()
        @remove()
      false
    ```
- Now that we're calling destroy our todo item's need to know their url
  - Inside the TodoItems Backbone collection we need to add a url function
  - ```
    url: => "todo_lists/#{@todoList.id}/todo_items"
    ```
- Let's wire up the Rails side
  - First add a new route
  - ```
    resources :todo_lists, only: [] do
      resources :todo_items, only: [:destroy]
    end
    ```
  - Then we add the TodoItemsController
  - ```
    class TodoItemsController < ApplicationController
      respond_to :json

      def destroy
        respond_with todo_item.destroy
      end

      private

      def todo_list
        TodoList.find(params[:todo_list_id])
      end

      def todo_items
        todo_list.todo_items
      end

      def todo_item
        todo_items.find(params[:id])
      end
    end
    ```
- We need to return to our Backbone app and make a few changes to get everything
  working correctly
  - We need to make sure the right todo_list_id value is being passed to Rails
  - To do this we need to return to our TodoList Backbone model
  - Modify the `parse` method to extract the todo list's id and store it in an
    attribute called todoListId
  - This is required because the id is actually a Note with a polymorphic body
    which is either a StickyNote or a TodoList
    - ```
      parse: (data) ->
        @todoItems.reset(data.body.todo_list.todo_items, parse: true)
        data.todoListId = data.body.todo_list.id
        delete data.body
        data
      ```
  - We want to use this new id rather than the id attribute in our url, so now
    we return to our TodoItems Backbone collection and modify the url method
  - ```
    url: => "todo_lists/#{@todoList.get('toddoListId')}/todo_items"
    ```
- We also don't want our app to persist blank todo items
  - First let's enforce this on the Rails side by adding a validation on the
    TodoItem Rails model
  - ```
    validates :title, presence: true
    ```
  - We also want to filter blank todo items from the json our Backbon app sends
    to the server
  - In the TodoList Backbone model we want to filter out any new todo item in
    the `toJSON` method
  - ```
    toJSON: ->
      {
        title: @get('title')
        body:
          type: 'todo_list'
          todo_list:
            todo_items: @todoItems.reject((i) -> i.isNew())
      }

    ```
