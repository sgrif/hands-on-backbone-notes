Converting to a Single Page Application
==

Since we've gone through all this trouble to never actually leave the page, why
are we going through even more trouble to make it look like we are?

Styling
--

First let's make things look more appropriate.

- We'll start by creating a style sheet at app/assets/stylesheets/notes.css.scss
  - Add a definition for the note class
  - ```
    .note {
      width: 225px;
      heigth: 225px;
      margin: 20px;
      float: left;

      border: 1px solid black;
      padding: 10px;

      list-style: none;
    }
    ```

- Next remove all the content from the show.jst.eco template
  - Wrap the title input in a header tag of class `title-container`
  - Wrap the content text area in div with class body
  - Ensure the existing classes remain on both input elements
  - ```
    <header class="title-container">
      <input type="text" class="note-title" placeholder="Title..." value="<%= @note.get('title') %>" />
    </header>
    <div class="body">
      <textarea class="note-content" placeholder="Content..."><%= @note.get('content') %></textarea>
    </div>
    ```

- Now we return to our new stylesheet
  - Add a definition for the title container class under the note
  - ```
    .title-container {
        height: 25px;

        margin-bottom: 10px;
        border-bottom: 1px solid black;

        .note-title {
          font-size: 17px;
          font-weight: bold;
        }
      }
    ```
  - Remove background and border for both inputs within a note
    - Also remove outline on :focus
    - ```
      input[type=text], textarea {
          border: none;
          background: none;

          &:focus {
            outline: none;
          }
        }
      ```
  - Add a definition for the body
    - Add a definition for title-content
    - ```
      .body {
          width: 100%;
          height: 165px;

          .note-content {
            width: 100%;
            height: 100%;
            resize: none;
          }
      ```

Improving the UX
--

- Open the ShowNote Backbone view
  - Remove the existing event handler methods and events hash values
  - Create a save method, which grabs the title and body from the inputs
  - Bind a `change` event with no target to that method
  - ```
    class App.Views.ShowNote extends Backbone.View
      template: JST['notes/show']

      className: 'note'

      events:
        'change': 'save'

      render: ->
        @$el.html(@template(note: @model))
        this

      save: ->
        @model.set
          title: @$('.note-title').val()
          content: @$('.note-content').val()
        @model.save()
        false
    ```
  - Title is a single line so it should save and blur when enter is pressed
    - Create a method called blurIfEnter and bind it to the `keydown` event
      - keyCode 13 means the enter button was pressed
      - ```
        events:
          'change': 'save'
          'keydown .note-title': 'blurIfEnter'
        ```
      - ```
        blurIfEnter: (e) ->
          if e.keyCode == 13
            @$(':input').blur()
        ```
  - To improve UX let's make it clear what is being edited
    - On focus of both of our note inputs bind an event called beginEditing
    - On blur of both of our note inputs bind an event called endEditing
    - ```
      events:
        'change': 'save'
        'keydown .note-title': 'blurIfEnter'
        'focus .note-title, .note-content': 'beginEditing'
        'blur .note-title, .note-content': 'endEditing'
      ```
    - Add the begingEditing method
      - Add a class called editing to the input that comes in focus
      - ```
        beginEditing: ->
          @$el.addClass('editing')
        ```
    - Add the endEditing method
      - Remove the class called editing from the input that goes out of focus
      - ```
        endEditing: ->
          @$el.removeClass('editing')
        ```
- Open the show.jst.ceo file
  - Add a div to the bottom of the page with class footer
  - Add a save button with a class of save
  - ```
    <div class="footer">
      <button class="save">Save</button>
    </div>
    ```
- Open the notes.css.scss file
  - Add a style for the footer class under note
  - Add a blue border to the note when the editing class is present
  - ```
    .footer {
      display: none;
    }

    &.editing {
      border-color: blue;

      .footer {
        display: block;
      }
    }
    ```

Clean Up
--

Because of our recent change we can remove the following files and code:

- The router
- The wildcard route in rails
- The Edit view and associated template

We also need to change the initialize method in our ScratchPad class to render the `Notes` view which
was preivously done in the index router.
