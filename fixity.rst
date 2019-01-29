.. _fixity:

======
Fixity
======

Fixity is an application for use with an Archivematica Storage Service
installation. Fixity is run from the command-line and will check the checksums
of AIPs in storage as a means of verifying the integrity of the bags.

Fixity can also be configured to POST reports to a remote service after
completing every scan. It also retains an internal database which keeps track of
every AIP it has scanned and a report of every scan. The Archivematica Storage
Service displays these reports in the Packages tab; see :ref:`reporting
<fixity_reporting>`, below.

*On this page*

* :ref:`Installation <fixity_install>`
* :ref:`Usage <fixity_usage>`
* :ref:`Reporting <fixity_reporting>`

.. _fixity_install:

Installation
------------

#. Checkout or link the code to `/usr/lib/archivematica/fixity` by navigating to
   `/usr/lib/archivematica/` and cloning the code.

   .. code-block:: bash

         user@root:~$ cd /usr/lib/archivematica/
         user@root:/usr/lib/archivematica/$ git clone https://github.com/artefactual/fixity.git

   .. tip::

      If you get a permissions error, try running the command as sudo:
      ``sudo git clone https://github.com/artefactual/fixity.git``

   Once this is complete, the directory `/usr/lib/archivematica/fixity` should
   exist. Navigate to the home directory:

   .. code-block:: bash

      user@root:/usr/lib/archivematica/$ cd ~

2. Next, switch to root:

   .. code-block:: bash

      user@root:~$ sudo -i

  Then run the following commands to create a virtualenv in
  `/usr/share/python/fixity` and install Fixity and dependencies in it.

  .. code-block:: bash

     root@host:~# virtualenv /usr/share/python/fixity
     root@host:~# source /usr/share/python/fixity/bin/activate
     (fixity)root@host:~# cd /usr/lib/archivematica/fixity
     (fixity)root@host:/usr/lib/archivematica/fixity# pip install -r requirements.txt
     (fixity)root@host:/usr/lib/archivematica/fixity# python setup.py install


3. Create a symlink from the executable to /usr/local/bin.  You must still be
   logged in as root.

   .. code-block:: bash

      (fixity)root@host:/usr/lib/archivematica/fixity# ln -s /usr/share/python/fixity/bin/fixity /usr/local/bin/fixity

4. Export the required environment variables. For ease of use later, creating
   `/etc/profile.d/fixity.sh` is recommended. To create the file, run the
   following commands:

   .. code-block:: bash

      (fixity)root@host:/usr/lib/archivematica/fixity# touch /etc/profile.d/fixity.sh
      (fixity)root@host:/usr/lib/archivematica/fixity# nano /etc/profile.d/fixity.sh

   You are now editing the environment variables file. You should use the URL of
   your Storage Service, and the username and API key of one of your Storage
   Service users. Replace the URL, user and key with your data.

   .. code-block:: bash

      #!/bin/bash
      export STORAGE_SERVICE_URL=http://localhost:8000
      export STORAGE_SERVICE_USER=myuser
      export STORAGE_SERVICE_KEY=myapikey

   Optionally, if you are using Fixity with a reporting service, you can also
   add the following:

   .. code-block:: bash

      export REPORT_URL=http://myurl.com
      export REPORT_USERNAME=myuser
      export REPORT_PASSWORD=mypassword

   Finally, load the variables from the file.

   .. code-block:: bash

      (fixity)root@host:/usr/lib/archivematica/fixity# source /etc/profile.d/fixity.sh

5. Run Fixity with sudo or as root the first time. Subsequent runs can be with
   any user.

   .. code-block:: bash

      (fixity)root@host:/usr/lib/archivematica/fixity# fixity scanall

6. Exit the virtualenv and root user:

   .. code-block:: bash

      (fixity)root@host:/usr/lib/archivematica/fixity# deactivate
      root@host:/usr/lib/archivematica/fixity#
      root@host:/usr/lib/archivematica/fixity# exit
      user@host:~$


7. After the initial install, to run Fixity just load the variables you defined
   above and set Fixity to scan all AIPs:

   .. code-block:: bash

      user@host:~$ source /etc/profile.d/fixity.sh
      user@host:~$ fixity scanall


.. _fixity_usage:

Usage
-----

Fixity is run from the command line after installation as per the instructions
above.

**Check fixity of all AIPs in storage**

Run the following command:

.. code-block:: bash

   fixity scanall

**Check fixity of a specific AIP**

Find the UUID of the AIP. This can be done by going to the Archival Storage tab
of the pipeline or finding the AIP in the Storage Service's Packages tab.
Once you have the UUID, run the following command, replacing the sample UUID
here with your AIP's UUID:

.. code-block:: bash

   fixity scan 229adfe9-c63b-4ebc-9428-0b9427b5862c

**Additional options**

The following flags can be used with the commands above:

* ``--throttle``: add time (in seconds) to wait when scanning multiple AIPs.
  This can help reduce extended disk load on the filesystem on which the AIPs
  reside.

* ``--force-local``: some types of Storage Service spaces (e.g. Arkivum) have a
  space-specific way of performing fixity checks. Using ``force-local`` forces
  the use of the Storage Service for the fixity check, rather than using the
  space's internal fixity method.

* ``debug``: add extra debugging output.

.. _fixity_reporting:

Fixity reporting
----------------

Fixity checks will be reported in the Packages tab, in the Fixity Date and
Fixity Status columns:

.. image:: images/fixity_packages.*
   :align: center
   :width: 80%
   :alt: The packages tab in the Storage Service showing Fixity Date and Fixity Status.

Click on the Fixity status for an AIP (either "Failed" or "Success") to see the
history of fixity checks. The history page will show the time and date of the
fixity checks and the error, if any.

.. image:: images/fixity_error.*
   :align: center
   :width: 80%
   :alt: Fixity history page showing an error in fixity check.

:ref:`Return to the top <fixity>`
