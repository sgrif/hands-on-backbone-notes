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

- In the routes file add a call to resources
- ```
  resources :notes, only: [:index, :create, :update, :destroy]
  ```
- In order to keep Rails and Backbone from conflicting we also want to route any
  request to our notes#index action
- ```
  get '*any' => 'notes#index'
  ```
- Next we want to build out our Notes Rails controller to connect with our
  Backbone application
- ```
  class NotesController < ApplicationController
    helper_method :notes, :note
    respond_to :json, only: [:index, :create, :update, :destroy]
    respond_to :html, only: [:index]

    def index
      respond_with notes
    end

    def create
      note = Note.create(note_params)
      respond_with note
    end

    def update
      note.update_attributes(note_params)
      respond_with note
    end

    def destroy
      respond_with note.destroy
    end

    private

    def note_params
      params.permit(:title, :content)
    end

    def notes
      @_notes ||= Note.all
    end

    def note
      @_note ||= notes.find(params[:id])
    end
  end
  ```
