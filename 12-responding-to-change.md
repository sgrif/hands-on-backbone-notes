Responding to Server-side Changes
==

- Now we'll add last updated to footer div of our note's show template
  - Change the name of the existing `footer` class to `editing-footer`
  - Add a new div with the class `normal-footer` to the note div
  - ```
    <div class="normal-footer"></div>
    <div class="editing-footer">
      <button class="save">Save</button>
    </div>
    ```
  - Modify the notes.css.scss style sheet to reflect the newly named footer
    class
  - Add a new class definition for `normal-footer`
    - ```
      .normal-footer {
        color: gray;
        border-top: 1px solid black;
        padding-top: 10px;
        font-size: 0.8em;
      }
      ```

- Add momentjs to handle the display of the dates
- Add momentjs-rails to the gem file
- Bundle
- Add momentjs to the application.js file

- We would prefer to avoid adding a lot of additional logic to this view, it's
  getting bloated
- Let's create a new view called `LastUpdated`
- This view will have no template instead we'll use only a render method
  - This method will only replace the views element with it's model's updated\at
  - ```
    class App.Views.LastUpdated extends Backbone.View
      render: =>
        @$el.html(@model.get('updated_at'))
        this
    ```
- Now let's go back to our `ShowNote` method
  - In the initialize method we'll create a new instance of our `LastUpdated`
    view passing out model
  - ```
    initialize: ->
      @listenTo(@model, 'invalid', @addError)
      @listenTo(@model, 'error', @addError)
      @lastUpdated = new App.Views.LastUpdated(model: @model)
    ```
  - in the render method the setElement on our newly instantiated `LastUpdated`
    object initialized above
  - Pass the setElement method the normal-footer div from our show.jst.eco
    template using a jQuery selector $('.normal-footer')
    - Always keep in mind, using this setElement approach will make any of the id,
      className, tagname or attributes will not be applied to the element that is passed in
    - ```
      render: ->
        @$el.html(@template(note: @model))
        @lastUpdated.setElement(@$('.normal-footer')).render()
        this
      ```
  - Because we call render in a subview we need to override the remove method
    - In the overriden remove we need to call the remove method on the instance
      of `LastUpdated` we rendered
    - If we forget to do this it will lead to a memory leak
    - ```
      remove: ->
        @lastUpdated.remove(arguments...)
        super(arguments...)
      ```
- Now we return to our `LastUpdated` view
  - Add a method called lastUpdated
  - In this method we take advantage of the moment library we added
  - Pass the model's updated\_at value to the moment function
    - Call the calendar method on the object returned from the moment function
  - In the render method replace the `@model.get(@updated_at)` with a call to
    the new lastUpdated method
  - ```
    class App.Views.LastUpdated extends Backbone.View
      render: =>
        @$el.html(@lastUpdated())
        this

      lastUpdated: ->
        moment(@model.get('updated_at')).calendar()
    ```

- Now we want to make the timestamp update when we update the model on the
  server
- In the `LastUpdated` view's initialize method call `@listenTo(@model, 'change:updated_at', @render)`
  - This tells the view to call the render method whenever the updated\_at
    attributes changes on the model
  - ```
    initialize: ->
      @listenTo(@model, 'change:updated_at', @render)
    ```
- In order to make this new behavior work we need to modify our notes controller
  - In the rails controller, return the model `to_json` if it saves, respond to
    returns 204 no content by default
    - ```
      respond_with(note) do |format|
        format.json { render json: note }
      end
      ```
  - This is necessary because by default rails doesn't return the json
    representation of the updated model for updates
- Everything is updating properly

- Commit!
