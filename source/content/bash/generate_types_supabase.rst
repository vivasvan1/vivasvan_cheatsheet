Generate Supabase TS Datatype using CLI
=============================================

Open a terminal in the project root directory and run the following command.

.. code-block:: bash

    $ supabase login

This will open a browser window and ask you to login to Supabase. After you
login, you can close the browser window and return to the terminal and run the
following command.

.. code-block:: bash

    $ supabase gen types typescript --project-id your-project-id > lib/database.types.ts

This will generate a file called ``database.types.ts`` in the ``lib`` directory.
