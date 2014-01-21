Adding Application Functionality
==

In preparation for adding todo lists, let's separate the common element between
todos and notes (the title) from the piece that's different (the content)

Setting up the JSON
--

- `rails g model StickyNote content:string`
  - Add polymorphic body association to note
  - Create record for each note with the appropriate association and title set
- Add content method to `Note` which delegates to `body`
- Update index method
  - Update html view
    - `.to_json(methods: :content)`
  - Check things are working

- Update seeds

Handling the association
--

- I'd still like to just have a `Note` model in the JS side, but we need to
  handle the new API structure
- Change the JSON to include `body`
- Override the parse method in the model
- Maintain the same data structure we had before
- Need to pass parse: true to the constructor of the collection

- Commit!
  - end rendering-association
