Dates
==

- Add last updated to footer div
  - Hide when editing
  - Gray, add border, a bit of padding, shrink font
  - Leave blank

- Not exactly readable
- Grab momentjs to parse, use `calendar` for display
- Don't forget to add require to application.js and restart server

- Would prefer to avoid adding a lot of additional logic to this view, it's
  getting bloated
  - Note the irony that in Backbone, views tend to be fat and models tend to be
    skinny
- Create a view called `LastUpdated`, and construct it in the initializer with the
  same model
- Call `setElement` in render
  - Don't forget to return `this`
- Overload remove, and call remove on `@lastUpdated`
  - Mention those pesky memory leaks

- Lots of little helper views such as this tend to crop up and be reused.

- Still not updating
- in the constructor: `@listenTo(@model, 'change:updated_at', @render)`
- In the rails controller, return the model `to_json` if it saves, respond to
  returns 204 no content by default
  - ```
    respond_with(note) do |format|
      format.json { render json: note }
    end
    ```
- Everything is updating properly

- Commit!
  - end updated-at
