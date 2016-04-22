# Deleting messages in the guestbook

Your next task will be to add a new feature to the guestbook that allows administrators to delete messages.

# Logging in as an administrator

To log in as an admin, first log out (if you are logged in) then log in again. Check the "Sign in as Administrator" box.

# Adding delete links

Below each message in the guestbook there should be a delete link that is only displayed if the user is an admin.

In your index.html file, add this inside the for-loop below the greeting content to create the link:
```
<a href="/delete?guestbook_name={{ guestbook_name }}&greeting_id={{ greeting.key.id() }}">Delete</a>
```

Now, make it so it only displays when the user is an admin. This requires two steps:

1. In guestbook.py (or main.py if you named it that), pass in a new parameter to the template to indicate if the user is an admin.
You'll need to add a new entry to the "template_values" dictionary in your MainPage class with a boolean value (true for admin, false if not).
You can check if the user is an admin with this code in Python:

        users.is_current_user_admin()

2. In index.html, add an if-statement to the template around the link.
The if-statement should evaluate to true if the user is an admin.
See the other if-statement for reference on syntax.

# Adding delete handler

Now you'll write the code to actually handle deleting the message when the admin user clicks the link.

To do this, add a new handler class:

```
class Delete(webapp2.RequestHandler):
    def get(self):
        guestbook_name = self.request.get('guestbook_name',
                                          DEFAULT_GUESTBOOK_NAME)
        greeting_id = int(self.request.get('greeting_id'))
        
        # Fill in code here to delete.
        
        query_params = {'guestbook_name': guestbook_name}
        self.redirect('/?' + urllib.urlencode(query_params))
```

Next, map the "/delete" URL to the Delete handler by using this code at the bottom of your file:

```
app = webapp2.WSGIApplication([
    ('/', MainPage),
    ('/sign', Guestbook),
    ('/delete', Delete),
], debug=True)
```

Now go back and start filling in the code.

The code you write for this part should do 3 things:

1. Make sure the user is an admin using this code:

        users.is_current_user_admin()
If not then nothing should be deleted.

2. Retrieve the message to delete by its ID.
You'll need to use [Greeting.get_by_id](https://cloud.google.com/appengine/docs/python/ndb/modelclass#Model_get_by_id) to read from the datastore.
The ID you'll pass to this will be the greeting_id.

  Tricky part: You need to pass "parent" to Greeting.get_by_id.
  In this case, it will be:
  parent=guestbook_key(guestbook_name).
  
  The app, namespace, and ctx_options parameters are all optional and can be ignored here.

3. If the greeting is found (if it's not None) then delete it.
See this documentation for notes on how to delete entities from the datastore:
https://cloud.google.com/appengine/docs/python/ndb/entities#deleting_entities
