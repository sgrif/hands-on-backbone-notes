Setting up the JSON api
--

If we want to persist our changes, the first thing we'll need to do is have the
index page display what's in the database rather than using our hard coded
array.

Let's make sure that our index action is set up to return JSON. We need to add
the appropriate routes back into Rails, but we don't want to trample on
Backbone for non-json requests. Unfortunately, there's no good way to tell Rails
to only use a route for JSON requests (we can come close with constraints, but
they ignore the Accept header). One option would be to prefix all of our API
routes with /api, but for now let's rely on the fact that we only need index,
update, and create, none of which conflict with Backbone.

- `resources :notes, only: [:index, :create, :update, :destroy]`
- Ensure we have the appropriate boilerplate for those methods on the
  controller.
  - Use `respond_with`
  - Don't forget, we're using `strong_params`
    - Just call `permit`, not `require`
    - Backbone doesn't wrap attributes in a root key
- `rake db:reset`
