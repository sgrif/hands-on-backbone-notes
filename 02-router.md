The Rails Way
--

- Remove 'Hello From Backbone' alert in
  app/assets/javascripts/scratch\_pad.js.coffee
- Create controller at app/controllers/notes\_controller.rb
  - Create notes and note helper methods
- Generate model with title and content
  - Seed 3 notes
- Add resources line to config/routes.rb
- Change root
- Create view at app/views/notes/index.html.erb
  - ul, loop
    - li>dl>(dt,dd)\*2
      - Title/content
  - Title links to show
- Create view at app/views/notes/show.html.erb
  - Title as h1, content as p, back link
- Commit!
  - end-rails-way

Backbone Router
--

- It's a bit like Sinatra, but for client side javascript
- Remove all rails routes except root
  - Add wildcard route to application#index
- Create ScratchPadRouter in
  `app/assets/javascripts/router/scratch_pad_router.js.coffee`
  - Briefly mention that CoffeeScript provides a class system, and we'll be
    talking more about it later
  - Talk about Extends
  - Define routes hash
    - Demonstrate that either a method name or a function can be passed
  - Define index method with alert saying it is index
  - Define showNote method with alert mentioning the ID
    - String interpolation!
- Create new router in initialize method
- Add Backbone.history.start
  - Demonstrate how by default this only works with #notes/1 and not notes/1
  - Add pushState: true
    - Demonstrate that standard URLs now work
    - Demonstrate that hash URLs still work
    - Mention the dangers of forgetting `hashChange: false` for non-single page
      apps
- Commit!
  - end-backbone-router
