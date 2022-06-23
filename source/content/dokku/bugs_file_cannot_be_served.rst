The page you were looking for cannot be served. If you are the application owner check the logs for more information.
======================================================================================================================


When:
------
    when trying to save model with image data.

Cause:
------
    nginx client_max_body_size is by default 1MiB but the image gets added to body is >1MiB.

Fix:
-----
    .. code-block:: bash
        
        $ dokku nginx:set app-name client-max-body-size 50m