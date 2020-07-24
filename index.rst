.. storage_service documentation master file, created by
   sphinx-quickstart on Sun Feb 17 11:46:20 2013.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _index:

===========================================
Archivematica Storage Service documentation
===========================================

The Storage Service is used alongside Archivematica to manage the storage
locations and packages for one or many Archivematica dashboards.

For information about Archivematica, including user and administrator manuals,
please see the :ref:`Archivematica documentation <archivematica:home>`.

.. _storage-service-administration:

Administering the Storage Service
---------------------------------

This section provides information for Storage Service administrators. This
manual is intended for systems administrators who are responsible for
installing, configuring, setting up, and maintaining Archivematica and related
systems.

* :ref:`Administering the Storage Service <administrators>`
* :ref:`Storage Service glossary and organization <organization>`
* :ref:`Archivematica configuration <archivematica-configuration>`
* :ref:`Pipelines <pipelines>`
* :ref:`Spaces <spaces>`
* :ref:`Locations <locations>`
* :ref:`Packages <packages-tab>`
* :ref:`Administration <administration>`

.. _storage-service-aip-recovery:

AIP recovery
------------

AIP recovery support allows a storage service administrator to replace a corrupt
version of a stored AIP with a correct version (restored from a backup, for
example).

* :ref:`AIP recovery <recovery>`
* :ref:`Performing an AIP recovery <how-recovery-works>`
* :ref:`Example recovery request <example-recovery-request>`
* :ref:`Reporting recovery progress to external systems <reporting-recovery-progress>`

.. _storage-service-fixity:

Fixity
------

The Archivematica Storage Service exposes an `API endpoint`_ that allows users
to check fixity on individual AIPs. `Fixity`_ is also an application for use
with the Archivematica Storage Service. The Fixity application is run from the
command-line and uses the API endpoint to perform batch checking across the
entirety of the AIP storage location.

* :ref:`Fixity <fixity-docs>`
* :ref:`Checking fixity using the API endpoint <using-fixity-endpoint>`
* :ref:`Using the Fixity application <fixity-application>`
* :ref:`Reviewing fixity results in the Storage Service <reviewing-fixity-checks>`

.. _storage-service-installation:

Installation
------------

This section describes how to install the Storage Service manually.

.. note::
   Most users install the Storage Service alongside Archivematica from Ansible
   or packages using the :ref:`Archivematica general installation
   <archivematica:installation>` documentation.

* :ref:`Manually installing the Storage Service <install>`

:ref:`Back to the top <index>`

.. _`API endpoint`: https://wiki.archivematica.org/Storage_Service_API#Check_fixity
.. _`Fixity`: https://github.com/artefactual/fixity
