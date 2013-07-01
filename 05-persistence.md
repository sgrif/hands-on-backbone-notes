Adding Persistence
==

If we want to persist our changes, the first thing we'll need to do is have the
index page display what's in the database rather than using our hard coded
array.

Setting up the JSON api
--

Let's make sure that our index action is set up to return JSON. We need to add
the appropriate routes back into Rails, but we don't want to trample on
Backbone for non-json requests. Unfortunately, there's no good way to tell Rails
to only use a route for JSON requests (we can come close with constraints, but
they ignore the Accept header). One option would be to prefix all of our API
routes with /api, but for now let's rely on the fact that we only need index,
update, and create, none of which conflict with Backbone.

- `resources :notes, only: [:index, :create, :update]`
- Ensure we have the appropriate boilerplate for those methods on the
  controller.
  - Use respond\_with
  - Don't forget, we're using strong\_params
- Move our hard coded data back to db/seeds.rb
  - remove id
- `rake db:reset`

- Demonstrate (briefly) how we could do this with a `$.ajax` call to /notes, and
  what a pain it'd be if we did it that way

Luckily, Backbone again provides some plumbing to do the heavy lifting of this
for us in the form of Collections.

- Show that we could create an anonymous `Backbone.Collection`, passing it a url
  and model, then create a `Collections.Notes` class
- Set `AllNotes` to our new collection class, and call fetch on it

Events on models and collections
--

Our view is now blank, because our collection is loading asynchronously, and
will be empty when it gets passed to the view.

- Wrap the call to `ScratchPad.initialize` in a success callback to fetch
- Need to change loop in template to `@notes.models`
- Note that we can now remove the `rootUrl` property from `Models.Note`, as it's
  smart enough to get it from the collection
  - Every model knows that it's a part of a collection, and we can access it via
    the collection property.
- Edit is now broken, as well, since we don't access collections as arrays.
  - Change the router to call `.get` on the collection.
  - We don't need to do `id - 1`, since `get` takes an id, not an index.

Actually persisting
--

Now for the magic. All we need to do in order to make our views save is change
the call to `set` to `save`, passing in the same arguments.

- Demonstrate that we're now persisting our changes across page refreshes.
- Note that we're sending the whole resource, including unpermitted params (open
  the log to demonstrate that id, created\_at, and updated\_at were all passed.
- We can pass `patch: true`, to the save method to only update the changed
  attributes.
  - This will only work with Rails 4, since it *actually* sends an HTTP PATCH request.
  - Also note that "changed attributes" means the attributes passed to save.
    Anything changed using `set` will not be sent.

- Commit!
  - end persistence
