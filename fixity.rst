.. _fixity-docs:

======
Fixity
======

Checking fixity is the process of ensuring that digital files have not changed
unexpectedly over time. The Archivematica Storage Service exposes an API
endpoint that enables users to check the fixity of individual AIPs in storage.
Users can access the API endpoint directly or by using the Fixity application
developed by Artefactual.

Archivematica AIPs are packaged according to the `BagIt File Packaging Format`_.
The fixity API endpoint verifies the bag by validating the contents of the bag
against the checksums and payload oxum listed in the bag files. This can tell
users if files have been modified, deleted, or added to the AIP.

For more information about fixity and checksums, see the Digital Preservation
Coalition's `Digital Preservation Handbook`_.

*On this page:*

* :ref:`Checking fixity using the API endpoint <using-fixity-endpoint>`
* :ref:`Using the Fixity application <fixity-application>`
* :ref:`Reviewing fixity results in the Storage Service <reviewing-fixity-checks>`

.. _using-fixity-endpoint:

Checking fixity using the API endpoint
--------------------------------------

The fixity API endpoint can be used to scan a single AIP. It will return a
success or failure, as well as any error messages. More information about the
endpoint can found found in the `Storage Service API documentation`_.

.. _fixity-application:

Using the Fixity application
----------------------------

`Fixity`_ is also an application for use with the Archivematica Storage
Service. Fixity is run from the command-line and uses the API endpoint
documented above to perform batch checking across the entirety of the AIP
store.

See the `Fixity`_ documentation for more information on its installation and
usage.

.. _reviewing-fixity-checks:

Reviewing fixity results in the Storage Service
-----------------------------------------------

The result of a fixity check is written back to the Storage Service and shown in
the Packages tab of the user interface in the **Fixity Date** and **Fixity
Status** columns.

.. image:: images/fixity_packages.*
   :align: center
   :width: 80%
   :alt: The Fixity Date and Fixity Status columns are displayed in the

The fixity status is displayed as either "Success" or "Failed". For more
information about the check, click on the status of the AIP. The fixity check
information page will show the date and time that the fixity check was run, as
well as any error output resulting from the check. It will also show a full
history of fixity checks run on the AIP.

.. image:: images/fixity_error.*
   :align: center
   :width: 80%
   :alt: The fixity check information page showing the results of one fixity scan, which resulted in an error.

In the example shown in the screenshot above, the fixity scan resulted in an
error.

.. code::

   Oxum error. Found 8 files and 71101 bytes on disk; expected 8 files and
   71100 bytes.

In this case, an extra byte has been added to one of the files, so the payload
oxum in the ``bag-info.txt`` file is no longer correct. The next step for the
user would be to retrieve the AIP and investigate the files to see what has
changed.

:ref:`Return to the top <fixity-docs>`

.. _Archivematica AIP: https://www.archivematica.org/en/docs/archivematica-1.9/user-manual/archival-storage/aip-structure/#bagit-documentation
.. _BagIt File Packaging Format: https://datatracker.ietf.org/doc/html/rfc8493
.. _API endpoint: https://wiki.archivematica.org/Storage_Service_API#Check_fixity
.. _Fixity: https://github.com/artefactual/fixity
.. _Digital Preservation Handbook: https://www.dpconline.org/handbook/technical-solutions-and-tools/fixity-and-checksums
.. _Storage Service API documentation: https://wiki.archivematica.org/Storage_Service_API#Check_fixity
