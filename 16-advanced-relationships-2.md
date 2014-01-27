Advanced Model and Relationships - Part 2
==

Rendering todo lists
--

- First create a serializer for `TodoList`
  - Inside the serializer include the id
  - Add has\_many todo\_items as well
  - ```
    class TodoListSerializer < ActiveModel::Serializer
      attributes :id

      has_many :todo_items
    end
    ```
- Next we're going to create Backbone Collection and Model objects for todo
  items
  - For now simply create an empty TodoItem model
  - ```
    class App.Models.TodoItem extends Backbone.Model
    ```
  - The TodoItem collection class will only reference the TodoItem model
  - ```
    class App.Collections.TodoItems extends Backbone.Collection
      model: App.Models.TodoItem
    ```
- Override our Backbone TodoList model's constructor
  - Create a todoItems attribute and assign an instance of the Backbone TodoItem
    collection object
  - ```
    constructor: ->
      @todoItems = new App.Collections.TodoItems([], todoList: this)
      super(arguments...)
    ```
- In the parse method reset the todoItems attribute to the values passed in the
  data argument
  - ```
    parse: (data) ->
      @todoItems.reset(data.body.todo_list.todo_items, parse: true)
      delete data.body
      data
    ```
- Create a toJSON method to translate the data back to the format provided by
  the server
  - ```
    toJSON: ->
      {
        title: @get('title')
        body:
          type: 'todo_list'
          todo_list:
            todo_items: @todoItems.toJSON()
      }
    ```
- Now we need to create a TodoItemsIndex Backbone view
  - Set the tagName attribute to `ul`
  - Set the className attribute to `todo-items`
  - For now we'll leave the render method blank
  - ```
    class App.Views.TodoItemsIndex extends Backbone.View
      tagName: 'ul'
      className: 'todo-items'

      render: =>
        @$el.html('')
        this
    ```
- Next we create a singular TodoItem Backbone view
  - Set the className attribute to `todo-item`
  - In the render method display the model's title
  - ```
    class App.Views.TodoItem extends Backbone.View
      className: 'todo-item'

      render: =>
        @$el.html(@model.get('title'))
        this
    ```
- Once we're done with our TodoItem Backbone view we need to add some code to
  our TodoItemsIndex Backbone view
  - In the render method we want to iterate over our collection of TodoItems and
    render the TodoItem Backbone view for each item
  - ```
    render: =>
      @$el.html('')
      @collection.forEach(@renderItem)
      this
    ```
  - To do this we need to add a method named `renderItem` that takes a TodoItem
    model as an argument
    - In the renderItem method we need to instantiate an instance of the
      TodoItem Backbone view and append the rendered view to the TodoItemIndex's
      DOM object
    - ```
      renderItem: (model) =>
        view = new App.Views.TodoItem(model: model, tagName: 'li')
        @$el.append(view.render().el)
      ```
  - Finally we need to override the TodoItemIndex Backbone view's initialize
    method to tell the object to rerender when an item is added to the
    collection, or when the collection is reset
  - ```
    initialize: ->
      @listenTo(@collection, 'add', @renderItem)
      @listenTo(@collection, 'reset', @render)
    ```
- Now we return to our TodoList Backbone view
  - First we override the initializer to instantiate an instance of the
    TodoItemIndex class
  - We pass the TodoList's model's todo_items as the TodoItemIndex's collection
  - ```
    initialize: ->
      @todoItems = new App.Views.TodoItemsIndex(collection: @model.todoItems)
    ```
  - Next we modify the render method to render the TodoItemIndex view we
    instantate in the initalizer
  - ```
    render: ->
      @$el.html(@todoItems.render().el)
      this
    ```
  - Finally we have to tell the TodoList Backbone view to remove the newly
    instantiated TodoItemIndex when anytime it gets removed to avoid a memory
    leak
  - ```
    remove: ->
      @todoItems.remove(arguments...)
      super(arguments...)
    ```
- Now that we're printing out todo list items let's add some styles
  - Open the note.css.scss file
  - Inside the .body class definition add a definition for the .todo-items class
  - ```
    .todo-items {
      padding: 0;

      .todo-item {
        list-style: none;
        }
      }
    ```

- Now we'll make the todo items in the list editable
- Create a new todo item show template
  `app/assets/templates/todo\_items/show.jst.eco`
  - Inside the template add a single text input
  - ```
    <input type="text" class="todo-item-title" value="<%= @todoItem.get('title') %>" />
    ```
- Now we need to connect our new view to the TodoItem Backbone view
  - set the template attribute to `template: JST['todo\_items/show']`
  - Modify the render method to render the template instead of just printing out
    the model's title
  - ```
    render: =>
      @$el.html(@template(todoItem: @model))
      this
    ```
- Now that our template is complete and rendering we need to add a few more
  styles
  - Inside the .todo-item class definition add a definition for the
    .todo-item-title class
  - ```
    .todo-item-title {
      width: 85%;
    }
    ```
- In order to save todo items they need to be aware of the url where information
  needs to sent in order to save or update them
  - Inside the TodoItems Backbone collection we will add an initialize method that
    will set a todoList variable
    - ```
      initialize: (data, options) ->
          @todoList = options.todoList
      ```
    - Next we'll set the url attribute for the collection to a function that returns
      the todo list's todo items url
    - ```
      url => "/todo_items/#{@todoList.id}/todo_items"
      ```
  - Now that our TodoItems Backbone view's initalize method expects a todo list
    object we need to modify our TodoList Backbone model to send it
    - In the TodoList Backbone model modify the constructor to send the todoList
      to the TodoItems Backbone collection constructor
      - We have to pass an empty array as the first parameter because the first
        argument is always the data the collection should contain
      - ```
        constructor: ->
          @todoItems = new App.Collections.TodoItems([], todoList: this)
          super(arguments...)
        ```
- Now that our Backbone application is ready to send us the data required to
  save and update todo items we have to return to our Rails app
  - First we need to add routes
  - ```
    resources :todo_list, only: [] do
      resources :todo_items, only: [:create, :update]
    end
    ```
  - Now we need to create the controller we routed to
  - Create a new controller TodoItemsController
  - ```
    class TodoItemsController < ApplicationController
      respond_to :json

      def create
        respond_with TodoItem.create(todo_item_params)
      end

      def update
        todo_item.update_attributes(todo_item_params)
        respond_with todo_item
      end

      private

      def todo_item_params
        params.permit(:title, :complete)
      end
    end
    ```
- With routes and a controller in place we need to return to our Backbone
  application and tell it when to send the data to our server
  - In the TodoItem Backbone view we are going to bind to the change event of
    the text input
  - ```
    events:
      'change .todo-item-title, .todo-item-complete': 'saveModel'
    ```
  - We handle this event with a method called `saveModel` now we have to create
    this method
  - ```
    saveModel: ->
      @model.set
        title: @$('.todo-item-title').val()
      @model.save()
      false
    ```
  - Because we want our time stamp to update correctly we need to save the
    entire note
  - To do this we're going to override the save method on the TodoItem Backbone
    model
  - ```
    save: ->
      @collection.todoList.save()
    ```
  - The TodoItems Backbone collection will need to know about the todo list to make
    this work
    - In the TodoItem Backbone collection add an initalize method and set a
      todoItems attribute
    - ```
      initialize: (data, options) ->
        @todoList = options.todoList
      ```
- Now that our Backbone application is sending data to our Rails application we
  need to make a few changes to the NoteForm.rb class
- We need to expand the accepted params to include todo lists and items as well
  as sticky notes
- ```
  class NoteForm < Struct.new(:note, :params)
    def save
      note.body ||= body_class.new
      note.attributes = note_attributes
      note.body.attributes = permitted_body_attributes
      note.save
    end

    private

    def body_type
      params[:body][:type]
    end

    def body_class
      body_type.classify.constantize
    end

    def note_attributes
      params.permit(:title)
    end

    def normalized_body_attributes
      if body_type == 'todo_list'
        body_attributes[:todo_items_attributes] = body_attributes.delete(:todo_items)
      end
      body_attributes
    end

    def permitted_body_attributes
      normalized_body_attributes.permit(*permitted_attributes[body_type])
    end

    def body_attributes
      params.require(:body).require(body_type)
    end

    def permitted_attributes
      {
       'sticky_note:' => [:content],
       'todo_list' => [todo_items_attributes: [:id, :title, :complete]]
      }
    end
  end
  ```
- The last thing we need to do is modify our TodoList Rails model to accept
  attributes for TodoItems
  - In the TodoList Rails model add the following call
  - ```
    accepts_nested_attributes_for :todo_items
    ```

- If a todo item is completed we would like to disable editing
  - In our TodoItem Backbone view we need to add a new method called
    `setDisabled`
  - The method sets the disabled attribute on the todo item title's text box to
    the values of the model's complete attribute
  - ```
    setDisabled: ->
      @$('.todo-item-title').attr('disabled', @model.get('complete'))
    ```
  - We need to call our new method from the render method
  - ```
    render: =>
      @$el.html(@template(todoItem: @model))
      @setDisabled()
      this
    ```
  - We can add additional styles to mark through completed todos as well
    - In the todo-item-title class definition add a style for disabled
    - ```
      &:disabled {
        text-decoration: line-through;
      }
      ```

- Finally let's add a checkbox to make it possible to complete a todo item
  - Add a checkbox to the todo item show template
  - ```
    <input type="checkbox" class="todo-item-complete" />
    <input type="text" class="todo-item-title" value="<%= @todoItem.get('title') %>" /> 
    ```
  - Next we need to set the checkbox based on the todo item's complete attribute
  - In the TodoItem Backbone view rename the `setDisabled` method to
    `renderComplete`
  - Add logic to set the checkbox value
  - ```
    renderComplete: ->
      @$('.todo-item-title').attr('disabled', @model.get('complete'))
      @$('.todo-item-complete').attr('checked', @model.get('complete'))
    ```
  - Now we bind the change event for the todo-item-complete class to saveModal
  - ```
    events:
      'change .todo-item-title, .todo-item-complete': 'saveModel'
    ```
  - Add the complete attribute to the saveModel method
  - ```
    saveModel: ->
      @model.set
        title: @$('.todo-item-title').val()
        complete: @$('.todo-item-complete').is(':checked')
      @model.save()
      false
    ```
  - We need to rerender the view after save now
    - To do this we add an initilze method to the TodoItem Backbone view and
      listenTo the model's save event
    - ```
      initialize: ->
        @listenTo(@model, 'save', @render)
      ```
