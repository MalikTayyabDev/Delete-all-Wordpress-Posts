# Delete-all-Wordpress-Posts
Use as a temporary cleanup file to delete normal WordPress posts. It deletes posts in batches, so if there is a large number of posts, the server is less likely to timeout or crash.

Before running it, take a full files + database backup.

Upload it to:

/public_html/delete-all-posts.php

Before running, open the file and replace the secret key:

$secret_key = 'CHANGE_THIS_SECRET_123456';

Example:

$secret_key = 'my-secure-delete-key-982734';

Then run it using the same key in the URL:

https://yourdomain.com/delete-all-posts.php?key=my-secure-delete-key-982734

Important:

It permanently deletes posts.
It does not delete pages, products, or custom post types.
Do not share the URL publicly.
After completion, delete delete-all-posts.php immediately from the server.


