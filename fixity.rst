.. _fixity:

======
Fixity
======

Fixity is an application for use with an Archivematica/Storage Service installation. Fixity is run from the command-line and will check the checksums of AIPs in storage, by means of verifying the bags.

*On this page*

* :ref:`Installation <fixity_install>`
* :ref:`Usage <fixity_usage>`
* :ref:`Reporting <fixity_reporting>`

.. _fixity_install:

Installation
------------

1. Checkout or link the code to `/usr/lib/archivematica/fixity`

a. Go to `/usr/lib/archivematica/`

.. code-block:: none

     user@root:~$ cd /usr/lib/archivematica/

b. Clone the code:

.. code-block:: none

    user@root:/usr/lib/archivematica/$ git clone https://github.com/artefactual/fixity.git

.. tip::

   If you get a permissions error, try running the command as sudo: ``sudo git clone https://github.com/artefactual/fixity.git``

Once this is complete, the directory `/usr/lib/archivematica/fixity` should exist. `cd` back to the home directory

.. code-block:: none

   user@root:/usr/lib/archivematica/$ cd


2. Create a virtualenv in `/usr/share/python/fixity`, and install fixity and dependencies in it

a. Switch to root

.. code-block:: none

   user@root:~$ sudo -i

b. Run:

.. code-block:: none

    root@host:~# virtualenv /usr/share/python/fixity
    root@host:~# source /usr/share/python/fixity/bin/activate
    (fixity)root@host:~# cd /usr/lib/archivematica/fixity
    (fixity)root@host:/usr/lib/archivematica/fixity# pip install -r requirements.txt
    (fixity)root@host:/usr/lib/archivematica/fixity# python setup.py install


3. Create a symlink from the executable to /usr/local/bin.  You must still be root.

.. code-block:: none

   (fixity)root@host:/usr/lib/archivematica/fixity# ln -s /usr/share/python/fixity/bin/fixity /usr/local/bin/fixity


4. Export required environment variables. For ease of use later, creating `/etc/profile.d/fixity.sh` is recommended:

a. To create the file:

.. code-block:: none

    (fixity)root@host:/usr/lib/archivematica/fixity# touch /etc/profile.d/fixity.sh
    (fixity)root@host:/usr/lib/archivematica/fixity# nano /etc/profile.d/fixity.sh


b. You are now editing the environment variables file. You should use the URL of your Storage Service, and the username and API key of one Storage Service user. Replace the URL, user and key with your data.

.. code-block:: none

    #!/bin/bash
    export STORAGE_SERVICE_URL=http://localhost:8000
    export STORAGE_SERVICE_USER=myuser
    export STORAGE_SERVICE_KEY=myapikey


c. Optionally, if you are using Fixity with a reporting service, you can also add:


.. code-block:: none

    export REPORT_URL=http://myurl.com
    export REPORT_USERNAME=myuser
    export REPORT_PASSWORD=mypassword

d. Load the variables from the file.

.. code-block:: none

    (fixity)root@host:/usr/lib/archivematica/fixity# source /etc/profile.d/fixity.sh


5. Run the tool with sudo or as root the first time.  Subsequent runs can be with any user.

.. code-block:: none

  (fixity)root@host:/usr/lib/archivematica/fixity# fixity scanall


6. To exit the virtualenv:

.. code-block:: none

  (fixity)root@host:/usr/lib/archivematica/fixity# deactivate
  root@host:/usr/lib/archivematica/fixity#

And to exit the root user:

.. code-block:: none

  root@host:/usr/lib/archivematica/fixity# exit
  user@host:~$


7. After the initial install, to run fixity you only need to load the variables you defined earlier and run fixity.

.. code-block:: none

  user@host:~$ source /etc/profile.d/fixity.sh
  user@host:~$ fixity scanall


.. _fixity_usage:

Usage
-----


.. _fixity_reporting:

Fixity reporting
----------------

Fixity checks will be reported in the Packages tab, in the Fixity Date and Fixity Status columns:

.. image:: images/fixity_packages.*
   :align: center
   :width: 80%
   :alt: The packages tab in the Storage Service showing Fixity Date and Fixity Status.

Click on the Fixity status for an AIP (either "Failed" or "Success") to see the history of fixity checks. The history page will show the time and date of the fixity checks and the error, if any.

.. image:: images/fixity_error.*
   :align: center
   :width: 80%
   :alt: Fixity history page showing an error in fixity check.

:ref:`Return to the top <fixity>`
