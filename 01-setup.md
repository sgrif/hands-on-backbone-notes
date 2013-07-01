Create rails app
--

- Install Rails 4 RC
- `rails new scratch_pad`
- `cd scratch_pad`

Remove Turbolinks
--

- Delete line from Gemfile
- Delete line from app/assets/javascripts/application.js
- Remove "data-turbolinks-track" => true from stylesheet and javascript tags in
  app/views/layouts/application.html.erb

Install Backbone
--

- Add `gem 'backbone-on-rails', '~> 1.0.0'` to Gemfile
- `bundle install`
- `rails generate backbone:install`
- Examine app/assets/javascripts/application.js and note added files
- Examine app/assets/javascripts/scratch_pad.js.coffee
  - Explain that there are 4 components to Backbone, and that we create empty
    objects with those names for namespacing
  - CoffeeScript intro
    - Arrow = function() { }
    - Last statement of function is automatically returned
    - Parenthesis are optional
      - Add parenthesis to 'alert'
    - Note significant whitespace in $(document).ready ->
    - Note significant whitespace and lack of braces in root object definition

Verify setup
--

- `rails s`
- Add root route
  - Empty file at app/views/application/index.html.erb
  - Add `root to: 'application#index'` to config/routes.rb
- Visit localhost:3000 in browser
- Should see a popup saying "Hello From Backbone!"
- Commit!
