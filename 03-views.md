Backbone Views
--

- Add AllNotes array to ScratchPad
  - Just copy the hash used in seeds.rb
    - Remove commas, note that CoffeeScript can use newlines instead
  - Add ids
- Pass ScratchPad.AllNotes to Notes view
- `$('#container').html(index.render())`
- Create view file
- Notice that everything works with an empty class
  - Mention that the `render` function is noop by default
- Template options
  - One option that actually comes with backbone itself is \_.template. It works
    a lot like ERB, however you call it like
    `\_.template('<h1><%= title %></h1>)(title: 'Hello World')`
  - We're going to use something called 'eco', which the backbone-on-rails gem
    includes for us. It's literally erb, but for Coffeescript.
- `mv app/views/notes/index.html.erb app/assets/templates/notes.jst.eco`
- Change @notes.each to for note in @notes:
  - Note the requirement for colons, mention the requirement for if as well
  - Note that we need to keep the end block
- Remove link_to
  - Replace with a hard coded anchor tag and mention that we'll come back to it
- Add `template: JST['notes']`
- Add render method `@$el.html(@template(notes: @collection))`
  - Discuss what @$el is, mention the existance of @el and @$ as well
  - Note that @collection and @model are automatically present if passed to the
    constructor

- Demonstrate view loading properly
  - Note that link is not async
- Need to handle manually
  - Create events hash
  - Define event on click a
  - Note the ability to pass a method name or a function
  - Simply navigate to the href attribute
    - Note: Can't use this, need to do $(e.currentTarget)
- Change router for show to log id
  - Note the page doesn't change, the URL is modified
  - Note the need to pass trigger: true to navigate
- Demonstrate that history works

- Add EditNote view
  - Wire up to router
  - Write basic form in template
  - Add 'form submit' to events
    - Note the need to grab input values manually
      - Demonstrate how $(':input').serializeArray() could have been used instead
        - @model[input.name] = input.value for input in $(':input').serializeArray()
      - Navigate to root at end of method
  - Talk about how the page is updating even though nothing is being persisted

- If we're doing *really* well on time, do an aside on how this could be
  partially server side
  - If we wanted to use backbone for updates, but still decide what to render on
    the server side (and potentially keep our routing logic in Rails), note that
    we could still use backbone templates instead of Rails views, but place a line
    of inline JS in the view that instantiated a new backbone view, called render,
    and passed it the appropriate JSON for what to render. Demonstrate using code.

- Commit!
  - end backbone-views
