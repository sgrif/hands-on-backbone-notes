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

- `resources :notes, only: [:index, :create, :update, :destroy]`
- Ensure we have the appropriate boilerplate for those methods on the
  controller.
  - Use `respond_with`
  - Don't forget, we're using `strong_params`
    - Just call `permit`, not `require`
    - Backbone doesn't wrap attributes in a root key
- `rake db:reset`

- Demonstrate (briefly) how we could do this with a `$.ajax` call to /notes, and
  what a pain it'd be if we did it that way
  - Ironically, `_.map` actually makes it not too completely terrible
  - ```
    var notes
    notesPromise = $.ajax('/notes.json')
    noteFactory = function(data) { return new App.Models.Note(data) }
    notesPromise.done(function(data) { notes = _.map(data, noteFactory) })
    notes
    ```

Luckily, Backbone again provides some plumbing to do the heavy lifting of this
for us in the form of Collections.

- Create a `Collections.Notes` class, with a `url` and `model` property
- Set `AllNotes` to our new collection class, and call fetch on it

Events on models and collections
--

Our view is now blank, because our collection is loading asynchronously, and
will be empty when it gets passed to the view.

- Wrap the rest of `initialize` in a success callback to fetch
- Need to change loop in template to `@notes.models`
- Note that we can now remove the `urlRoot` property from `Models.Note`, as it's
  smart enough to get it from the collection
  - Every model knows that it's a part of a collection, and we can access it via
    the collection property.
- Edit is now broken, as well, since we don't access collections as arrays.
  - Change the router to call `.get` on the collection.
  - We don't need to do `id - 1`, since `get` takes an id, not an index.
- Check everything is still working
- Commit!
  - end json-api

Actually persisting
--

Now for the magic. All we need to do in order to make our views save is change
the call to `set` to `save`, passing in the same arguments.

- Demonstrate that we're now persisting our changes across page refreshes.
- Note that we're sending the whole resource, including unpermitted params (open
  the log to demonstrate that id, `created_at`, and `updated_at` were all passed.
  - The unpermitted parameters 'note' is because Backbone is submitting the
    model's attributes as a root level hash, rather than wrapped in a note
    key. Rails notices that we didn't pass the note key, so it tries to figure
    out what to wrap for us. This has a ton of gotchas, so I avoid using it. We
    could make Backbone conform to Rails, by adding this to the model: `toJSON: ->
    note: super()`, but I'd rather just turn `wrap_parameters` off, and work with
    the root params hash.
    - In the controller: `wrap_parameters false`
- We can pass `patch: true`, to the save method to only update the changed
  attributes.
  - This will only work with Rails 4, since it *actually* sends an HTTP PATCH request.
  - Also note that "changed attributes" means the attributes passed to save.
    Anything changed using `set` will not be sent.
  - Because of this, I prefer to just send the whole thing, and let
    `strong_parameters` log the unpermitted params. The unpermitted parameters
    are only logged in development by default. If the log lines bug you, you can
    change `permit(:title, :content)` to `slice(:title, :content).permit!`

- Commit!
  - end persistence
