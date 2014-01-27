Advanced JSON Input and Output
==

- Once we start passing options around to `to_json`, it's time to find a better tool for the job. We'll use `ActiveModel::Serializers` here.
- Add active\_record\_serializers to your Gemfile and bundle
- We need to disable root element
  - Add a new initializer with the follow code inside
  - ```
    ActiveModel::Serializer.root = false
    ActiveModel::ArraySerializer.root = false
    ```
- Create a new serializer title note_serializer
- Add the id, title, updated attributes in the new serializer
- Include the body as a `has_one` make sure to flag this as polymorphic
  - This tells rails to include the attributes under a partent attribute derived
    from the class name
- Create a helper method in application_helper.rb called `serialize`
- ```
  def serialize(models)
    ActiveModel::ArraySerializer.new(models).to_json
  end
  ```
- In index.html.erb change the code that serializing the objects to a call to
  our new serialize method `raw serialize(notes)`
- Now we need to change the parse method in our Backbone Note model
  - Map the content attribute from it's new location under the sticky_note
    attribute from our Rails app
  - We can remove the lines that delete the body_type and body_id attributes
  - ```
    parse: (data) ->
      data.content = data.body.sticky_note.content
      delete data.body
      data
    ```

Consistency
--

Now that we're sending our Backbone app json of a particular format we want to
make sure our Rails app can accept data in the same format.

- First let's tell Backbone how to translate the json data back to the format it
  received the data
  - In Backbone Note model override the function `toJSON`
  - This method will just return a JSON object
  - ```
    toJSON: ->
      {
        title: @get('title')
        body:
          type: 'sticky_note'
          sticky_note:
            content: @get('content')
      }
    ```
- Now we need to tell our Rails app how to accept this data
- First create a new file at app/models/note_form.rb
  - Create a class that's initializer takes a note and params
  - ```
    class NoteForm < Struct.new(:note, :params)
      def save
        note.body ||= StickyNote.new
        note.attributes = note_attributes
        note.body.attributes = body_attributes
        note.save
      end

      private

      def note_attributes
        params.permit(:title)
      end

      def body_attributes
        params.require(:body).require(:sticky_note).permit(:content)
      end
    end
    ```

Making it save
--

- Even though everything is rendering properly, saving is broken.
- We need to modify the relationship between the Note and the StickyNote to tell
  the StickyNote to save when its body changes
  - We do this by adding `autosave: true` to the `belongs_to` method call
- We also need to take advantage of our new NoteForm class in the notes
  controller in both the update and create actions

- Timestamp isn't updating if we only change the content. Unfortunately,
  `has_one` doesn't take `touch` as an option it only works with `belongs_to`
  - To fix this we'll add an `after_save` hook on `StickyNote`
    - In the afer_save hook call the touch method on the StickyNote's Note
  - In order to avoid making unnecessary queries add an `inverse_of` option to the `belongs_to` in `ScratchPadItem`

- Commit!
  - end updating-association

Creating and Destroying
--

- Add a create method to the controller, extract update to a `save_form_for`
  method.
- We get an error saying we can't build a one-to-one polymorphic association
- Add `scratch_pad_item.body ||= Note.new` to the beginning of our `save!`
- Add a destroy method to the controller
- Add `dependent :destroy` to the `belongs_to`
- Check that destroy is working
  method
- Check that create is working with just a title, just a body, and both

- Commit!
  - end associations
