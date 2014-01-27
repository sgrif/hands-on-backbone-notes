You will code along with Sean as he builds the application. Do what he does, pause the video if you need to catch up or explore something further. Hop into the chat room to ask questions as you have them, or send emails to learn@thoughtbot.com. We find that the act of doing is a very effective way of learning.

You should have access to a [GitHub repo for the workshop](https://github.com/thoughtbot/hands-on-backbone-js-on-rails). This repository is where you'll do your work for the app you'll build with Sean. Fork this private repo into your own Github Account.  If you don't know how to do this, there are [instructions on GitHub](https://help.github.com/articles/fork-a-repo)

By working in your own fork of this repository we'll be able to see the code you're writing, answer questions, and even comment on it. 

See the [README on GitHub](https://github.com/thoughtbot/hands-on-backbone-js-on-rails/blob/master/README.md) for more information.

Basic Setup and CoffeeScript
--

Create rails app
--

- Install Rails 4
- Generate a new Rails app `rails new scratch_pad`
- Navigate to the newly created rails app `cd scratch_pad`

Remove Turbolinks
--

- Delete the turbolinks gem from the Gemfile
- Delete the line that includes turbolinks from the app/assets/javascripts/application.js
- Remove "data-turbolinks-track" => true from stylesheet and javascript tags in
  app/views/layouts/application.html.erb

Install Backbone
--

- Add `gem 'backbone-on-rails', '~> 1.1.0.0'` to the Gemfile
- Add `gem 'lodash-rails', '~> 2.2.1'` to the Gemfile
- Install the bundle `bundle install`
- Install Backbone `rails generate backbone:install`
- Replace underscore with lodash in the app/assets/javascripts/application.js
- For simplicity add `window.App = window.ScratchPad` to
  app/assets/javascripts/scratch_pad.js.coffee
  - ```
    window.ScratchPad =
      Models: {}
      Collections: {}
      Views: {}
      Routers: {}
      initialize: ->
        alert('hello from backbone!');

    window.App = window.ScratchPad

    $(document).ready ->
      ScratchPad.initialize()
    ```

Verify setup
--

- Start the rails server `rails s`
- Add root route
  - Empty file at app/views/application/index.html.erb
  - Add the route `root 'application#index'` to config/routes.rb
- Visit localhost:3000 in browser
- Notice our greeting from Backbone "Hello From Backbone!"
- Commit!
