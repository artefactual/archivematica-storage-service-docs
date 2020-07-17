.. _fixity-docs:

======
Fixity
======

Checking fixity is the process of ensuring that digital files have not changed
unexpectedly over time. The Archivematica Storage Service exposes an API
endpoint that enables users to check the fixity of individual AIPs in storage.
Users can access the API endpoint directly or by using the Fixity application
developed by Artefactual.

The fixity API endpoint validates the contents of the AIP against the checksums
and payload oxum listed in the AIP's bag files. This can tell users if files
have been modified, deleted, or added to the AIP.

For more information about fixity and checksums, see the Digital Preservation
Coalition's `Digital Preservation Handbook`_.

*On this page:*

* :ref:`Checking fixity using the API endpoint <using-fixity-endpoint>`
* :ref:`Using the Fixity application <fixity-application>`
* :ref:`Reviewing fixity results in the Storage Service <reviewing-fixity-checks>`

.. _using-fixity-endpoint:

Checking fixity using the API endpoint
--------------------------------------



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
history of

.. image:: images/fixity_error.*
   :align: center
   :width: 80%
   :alt: Fixity history page showing an error in fixity check.

The `Archivematica AIP`_ is a Bag packaged in accordance with the
`Bagit specification`_, fixity checking is therefore a validation of the bag's
contents. An example can be seen in the error shown above:

.. code::

   Oxum error. Found 8 files and 71101 bytes on disk; expected 8 files and
   71100 bytes.



:ref:`Return to the top <fixity-docs>`

.. _Archivematica AIP: https://www.archivematica.org/en/docs/archivematica-1.9/user-manual/archival-storage/aip-structure/#bagit-documentation
.. _Bagit specification: https://tools.ietf.org/html/rfc8493
.. _API endpoint: https://wiki.archivematica.org/Storage_Service_API#Check_fixity
.. _Fixity: https://github.com/artefactual/fixity
.. _Digital Preservation Handbook: https://www.dpconline.org/handbook/technical-solutions-and-tools/fixity-and-checksums
