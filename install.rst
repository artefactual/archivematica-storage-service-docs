.. _install:

=======================================
Manually installing the Storage Service
=======================================

This page describes how to install the Storage Service manually; however, most
users install from Ansible or packages. Please see the :ref:`Archivematica
general installation <archivematica:installation>` documentation for
instructions on installing both Archivematica and the Storage Service from
Ansible or packages.

This app is designed to be run using `pip`_, `virtualenv`_, and
`virtualenvwrapper`_. It has been tested with Python 2.7.3.

To get up and running:

#. Install pip (if it is not already installed) ::

    $ sudo apt-get install python-pip

#. Install dependencies (if they are not already installed) ::

    $ sudo apt-get install gcc libxslt1-dev python-dev

#. Install virtualenv and virtualenvwrapper ::

    $ pip install virtualenv virtualenvwrapper

#. Add some environment variables to your shell startup script, such as .bashrc.
   This allows pip, virtualenv, and virtualenvwrapper to do their magic. The
   exact location of the virtualenvwrapper.sh script might be different on your
   system. ::

    export WORKON_HOME=$HOME/Envs
    source /usr/local/bin/virtualenvwrapper.sh

#. Create and configure the virtualenv, and add source code. ::

    mkdir -p $WORKON_HOME
    mkvirtualenv storage-service
    git clone git@github.com:artefactual/archivematica-storage-service.git
    cd archivematica-storage-service
    setvirtualenvproject

   You should now have a virtualenv, with it's own isolated python interpreter, and a project directory. The source code for the project should be there ready to work with.

#. Add dependencies. Assuming you are doing development work, use the local
   requirements file. For a testing or production install, use
   ``requirements/testing.txt`` or ``requirements/production.txt``,
   respectively. ::

    pip install -r requirements/local.txt

#. Create required environment variables. Add the following to your virtualenv's
   postactivate script, found in this example at
   ``$WORKON_HOME/storage-service/bin/postactivate``. Set the
   ``DJANGO_SETTINGS_MODULE`` to match whichever requirements file you installed
   from (local, test or production). ::

    PROJECTPATH=$(cat $VIRTUAL_ENV/.project)
    export PYTHONPATH=$PYTHONPATH:$PROJECTPATH

    export DJANGO_SETTINGS_MODULE=storage_service.settings.local
    export DJANGO_SECRET_KEY='ADDKEY'
    export SS_DB_NAME='storage_service/default.db'
    export SS_DB_USER=''
    export SS_DB_PASSWORD=''
    export SS_DB_HOST=''

#. Create the database schema. You can use the django-admin script to make an
   initial version of the main database, then use the tool South to create the
   FPR app's data model. ::

    cdproject
    cd storage_service
    python2 manage.py syncdb
    python2 manage.py migrate

#. Start the server. This example uses Django's built in webserver, good for
   testing only. Use a WSGI compliant webserver like Apache or Nginx in
   production. Using 0.0.0.0 makes the app available on all interfaces. ::

    cdproject
    cd storage_service
    python2 manage.py runserver 0.0.0.0:8000

:ref:`Back to the top <install>`

.. _pip: https://pypi.org/project/pip/
.. _virtualenv: https://virtualenv.pypa.io/en/latest/
.. _virtualenvwrapper: https://virtualenvwrapper.readthedocs.io/en/latest/
