Backbone Views
--

- Add `AllNotes` array to `ScratchPad` *in the initialize method*
  - Just copy the hash used in `seeds.rb`
    - Remove commas, note that CoffeeScript can use newlines instead
  - Add ids
- In the index route, pass ScratchPad.AllNotes to Notes view
- Add a container div to application layout
- Setup the index route in Backbone
  - `$('#container').html(index.render().el)`
- Create view file
- Notice that everything works with an empty class
  - Mention that the `render` function is noop by default
- Template options
  - One option that actually comes with backbone itself is `_.template`. It works
    a lot like ERB, however you call it like
    `_.template('<h1><%= title %></h1>')(title: 'Hello World')`
  - We're going to use something called 'eco', which the backbone-on-rails gem
    includes for us. ERB stands for Embedded Ruby. ECO is Embedded CoffeeScript,
    and the syntax will feel very familiar
- `mv app/views/notes/index.html.erb app/assets/templates/notes/index.jst.eco`
- Change `@notes.each` to `for note in @notes:`
  - Note the requirement for colons, mention the requirement for if as well
  - Note that we need to keep the end block
- Remove `link_to`
  - Replace with a hard coded anchor tag and mention that we'll come back to it
  - Build the `href` manually
- Add `template: JST['notes/index']`
- Add render method `@$el.html(@template(notes: @collection)); this`
  - Discuss what @$el is, mention the existence of @el and @$ as well
  - Note that @collection and @model are automatically present if passed to the
    constructor

- Demonstrate view loading properly
  - Note that link is not async
- Need to handle manually
  - Create events hash
  - Define event on click a
  - Note the ability to pass a method name or a function
  - Simply navigate to the href attribute
    - Note: Can't use `$(this)`, need to do `$(e.currentTarget)`
    - ```
      $this = $(e.currentTarget)
      Backbone.history.navigate($this.attr('href'))
      false
      ```
- Change router for show to log id
  - Note the page doesn't change, the URL is modified
  - Note the need to pass trigger: true to navigate
- Demonstrate that history works
  - No having to write a bunch of boilerplate for `pushState` and `popState`
  - Don't have to worry about strange bugs with navigating back and js view
    format being cached
    - Like `RailsCasts` had for ages

- Add `EditNote` view
  - Wire up to router
    - `id - 1` because zero-indexing
  - There's not really any reason to have a 'show' action for a single note, so
    we're going to combine show and edit
  - `tagName: 'form'`
  - Write basic form in template
  - Add 'submit' to events
    - Note the need to grab input values manually
      - Demonstrate how $(':input').serializeArray() could have been used instead
        - Only works if `name` exists on the inputs
        - `@model[input.name] = input.value for input in @$(':input').serializeArray()`
        - `
          for input in @$(':input').serializeArray()
            @model[input.name] = input.value
          `
      - Navigate to root at end of method
    - ```
      @model.title = @$('.title').val()
      @model.content = @$('.content').val()
      Backbone.history.navigate('/', trigger: true)
      false
      ```

  - Talk about how the page is updating even though nothing is being persisted
  - Demonstrate that we can go from page to page without hitting the server at
    all
  - Pages load wicked fast
  - Go back and forward as fast as possible and note that everything still works
    and keeps up

- If we're doing *really* well on time, do an aside on how this could be
  partially server side
  - If we have a lot of routing and SEO is a concern, then the rendering logic
    actually should stay on the server
  - If we wanted to use backbone for updates, but still decide what to render on
    the server side (and potentially keep our routing logic in Rails), note that
    we could still use backbone templates instead of Rails views, but place a line
    of inline JS in the view that instantiated a new backbone view, called render,
    and passed it the appropriate JSON for what to render. Demonstrate using code.

- Commit!
  - end backbone-views
