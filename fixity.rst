.. _fixity-docs:

======
Fixity
======

The Archivematica Storage Service exposes an `API endpoint`_ that allows users
to check fixity on individual AIPs. The result of a fixity check is written
back to the storage service and shown in the packages tab of the user-interface
in the Fixity Date and Fixity Status columns:

.. image:: images/fixity_packages.*
   :align: center
   :width: 80%
   :alt: The packages tab in the Storage Service showing Fixity Date and Fixity Status.

To see more detail, and the history of fixity checking completed on an
individual AIP, click on the Fixity status for the AIP (either "Failed" or
"Success"). The history page will show the time and date of the fixity checks
and previous error, if any.

.. image:: images/fixity_error.*
   :align: center
   :width: 80%
   :alt: Fixity history page showing an error in fixity check.

The `Archivematica AIP`_ is a Bag packaged in accordance with the
`Bagit specification`_, fixity checking is therefore a validation of the bag's
contents. An example can be seen in the error shown above:

      *Oxum error. Found 8 files and 71101 bytes on disk; expected 8 files and
      71100 bytes.*

Fixity Application
------------------

`Fixity`_ is also an application for use with the Archivematica Storage
Service. Fixity is run from the command-line and uses the API endpoint
documented above to perform batch checking across the entirety of the AIP
store.

See the `Fixity`_ documentation for more information on its installation and
usage.

:ref:`Return to the top <fixity-docs>`

.. _`Archivematica AIP`: https://www.archivematica.org/en/docs/archivematica-1.9/user-manual/archival-storage/aip-structure/#bagit-documentation
.. _`Bagit specification`: https://tools.ietf.org/html/rfc8493
.. _`API endpoint`: https://wiki.archivematica.org/Storage_Service_API#Check_fixity
.. _`Fixity`: https://github.com/artefactual/fixity
