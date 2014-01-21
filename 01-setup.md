You will code along with Sean as he builds the application. Do what he does, pause the video if you need to catch up or explore something further. Hop into the chat room to ask questions as you have them, or send emails to learn@thoughtbot.com. We find that the act of doing is a very effective way of learning.

You should have access to a [GitHub repo for the workshop](https://github.com/thoughtbot/hands-on-backbone-js-on-rails). This repository is where you'll do your work for the app you'll build with Sean. Fork this private repo into your own Github Account.  If you don't know how to do this, there are [instructions on GitHub](https://help.github.com/articles/fork-a-repo)

By working in your own fork of this repository we'll be able to see the code you're writing, answer questions, and even comment on it. 

See the [README on GitHub](https://github.com/thoughtbot/hands-on-backbone-js-on-rails/blob/master/README.md) for more information.

Basic Setup and CoffeeScript
--

Create rails app
--

- Install Rails 4
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

- Add `gem 'backbone-on-rails', '~> 1.1.0.0'` to Gemfile
- `gem 'lodash-rails', '~> 2.2.1'`
- `bundle install`
- `rails generate backbone:install`
- Replace underscore with lodash
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
  - Add `window.App = window.ScratchPad`

Verify setup
--

- `rails s`
- Add root route
  - Empty file at app/views/application/index.html.erb
  - Add `root 'application#index'` to config/routes.rb
- Visit localhost:3000 in browser
- Should see a popup saying "Hello From Backbone!"
- Commit!
