Local Postgres Setup
==========================

*Step1 - Install and Setup postgres*
------------------------------------------------------

First, run below command on the local machine (for Linux-based OS).

.. code-block:: bash

    $ sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib


Now you need to create a database, and link the database to the app.
You can do this from your own system:

Log-in to interactive postgres session

.. code-block:: bash

    $ sudo -u postgres psql

Create database for django project

.. code-block:: psql

    postgres=# CREATE DATABASE myproject;


Next, you will create a database user which you will use to connect to and interact with the database. Set the password to something strong and secure:


.. code-block:: psql

    postgres=# CREATE USER myprojectuser WITH PASSWORD 'password';

Afterwards, you will modify a few of the connection parameters for the user you just created. This will speed up database operations so that the correct values do not have to be queried and set each time a connection is established.

.. code-block:: psql

    postgres=# ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
    postgres=# ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
    postgres=# ALTER ROLE myprojectuser SET timezone TO 'UTC';

You are setting the default encoding to UTF-8, which Django expects. 
You are also setting the default transaction isolation scheme to “read committed”, which blocks reads from uncommitted transactions. Lastly, you are setting the timezone. By default, your Django projects will be set to use UTC. These are all recommendations from the Django project itself.


Now, all you need to do is give your database user access rights to the database you created:

.. code-block:: psql

    postgres=# GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;

Exit the SQL prompt to get back to the postgres user’s shell session:

.. code-block:: psql

    postgres=# \q



*Step2 - Configure the Django Database Settings*
------------------------------------------------------

Towards the bottom of the file, you will see a DATABASES section, change it as follows:


.. code-block:: python
    
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'myproject',
            'USER': 'myprojectuser',
            'PASSWORD': 'password',
            'HOST': 'localhost',
            'PORT': '',
        }
    }


Migrate your db
-----------------

.. code-block:: bash
    
    $ pip install psycopg2
    $ python3 manage.py makemigrations
    $ python3 manage.py migrate

Done!