Validations
--

Client side
--

- We've inadvertently made it possible to save empty notes.
- Let's make sure that notes have either title or body
- In our `Note` model we need to add a `validate` method
  - Add the logic required to ensure that in the event both title and content
    are blank something (and it can be anything) gets returned
  - Note that this only prevents saving if set and save are called separately

- In the `ShowNote` view add an initialize method
  - In the initialize method call the `listenTo` method to respond to the
   `invalid` event
  - ```
    initialize: ->
      @listenTo(@model, 'invalid', @addError)
    ```
  - Add a new method called `addError` to handle this event
  - in the `addError` method add a class called `error` to the note div
  - ```
    addError: =>
      @$el.addClass('error')
    ```
  - Modify the `beginEditing` method to remove the `error` class from the note div if it's present
  - ```
    beginEditing: ->
      @$el.addClass('editing')
      @$el.removeClass('error')
    ```
- Open the notes.css.scss file
  - define an error class under note
  - ```
    &.error {
      border-color: red;
    }
    ```

Server side
--

- Remove the validation method from the Backbone model
- Now let's move the validations to Rails model
- If we attempt to save Rails returns a 422
- Note that the `invalid` event is not triggered
- If in our `Note` view we bind to the `error` event instead of `invalid`
  Backbone behaves the same as it did for client side validations
- Now we add the validation method back to the Backbone model
- ```
  initialize: ->
    @listenTo(@model, 'invalid', @addError)
    @listenTo(@model, 'error', @addError)
  ```

- Commit!
