Adding Application Functionality
==

In preparation for adding todo lists, let's separate the common element between
todos and notes (the title) from the piece that's different (the content)

Setting up the JSON
--

- First we need to generate a new model for the content attribute
  - `rails g model StickyNote content:string`
    - Add polymorphic association to note and title it body
    - Next we need to create a migration that creates our new table and creates
      a new StickyNote for each Note
    - ```
      def change
        create_table :sticky_notes do |t|
          t.string :content

          t.timestamps
        end

        change_table :notes do |t|
          t.belongs_to :body, polymorphic: true
        end

        reversible do |dir|
          dir.down { add_column :notes, :content, :string }

          Note.reset_column_information
          Note.all.each do |note|
            dir.up { note.body = StickyNote.create(content:
    note.send(:read_attribute, :content)) }
            dir.down { note.send(:write_attribute, :content, note.body.content) }
            note.save!
          end

          dir.up { remove_column :notes, :content }
        end
      end
      ```
- Add a content method to `Note` which delegates to our new polymorphic
  assocation `body`
- To keep our application working we'll need to update our index.html.erb file
  - We need to tell Rails to include the content method when building the json
    representation of our Note model`.to_json(methods: :content)`

- We also need to update our seeds file to create a StickyNote for each Note

Handling the association on the client
--

- We would still like to just have a `Note` model in the JS side, but we need to
  handle the new API structure
- We need to change the JSON to include the `body`
  - We do this by adding a parse method to the Backbone Note model
  - ```
    parse: (data) ->
      data.content = data.body.content
      delete data.body
      delete data.body_type
      delete data.body_id
      data
    ```
  - Next we need to tell Backbone to call our new parse method
    - When we initialize our ScratchPad app we need to tell the notes collection
      to call parse `new @Collections.Notes(@notesJson, parse: true)`

- Commit!
