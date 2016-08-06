.. _administrators:

===========================================
Storage service- Administrator instructions
===========================================

The Archivematica Storage Service allows the configuration of storage spaces
associated with multiple Archivematica pipelines. It allows a storage
administrator to configure what storage is available to each Archivematica
installation, both locally and remote.

.. image:: images/SS1-0.*
   :align: center
   :width: 80%
   :alt: Home page of the Storage Service


*On this page:*

* :ref:`Storage Service entities and organization <organization>`

* :ref:`Archivematica Configuration <archivematica-configuration>`

* :ref:`Spaces <spaces>`

  * :ref:`Local filesystem <local-filesystem>`
  * :ref:`NFS <nfs>`
  * :ref:`Pipeline local filesystem <pipeline>`
  * :ref:`LOCKSS <admin-lockss>`
  * :ref:`DuraCloud <duracloud>`
  * :ref:`Arkivum <arkivum>`
  * :ref:`Swift <swift>`
  * :ref:`Fedora <fedora>`

* :ref:`Locations <locations>`

* :ref:`Pipelines <pipelines>`

* :ref:`Packages <packages>`

* :ref:`Administration <administration>`


.. _organization:

Storage Service entities and organization
-----------------------------------------

Pipelines
^^^^^^^^^

A pipeline refers to a single installation of an Archivematica dashboard.
The Storage Service can be used to configure spaces and locations across multiple Archivematica pipelines.

Spaces
^^^^^^

A space models a specific storage device. That device might be a locally-
accessible disk, a network share, or a remote system accessible via a protocol
like FEDORA, SWIFT, DuraCloud, or LOCKSS. The space provides the Storage Service
with configuration to read and/or write data stored within itself.

Packages are not stored directly inside a space; instead, packages are stored
within locations, which are organized subdivisions of a space.

Locations
^^^^^^^^^

A location is a subdivision of a space. Each location is assigned a specific
purpose, such as AIP storage, DIP storage, transfer source or transfer backlog,
in order to provide an organized way to structure content within a space.

Packages
^^^^^^^^

The Storage Service is oriented to storing packages. A "package" is a bundle of
one or more files transferred from an external service; for example, a package
may be an AIP, a backlogged transfer, or a DIP. Each package is stored in a
location.

.. _archivematica-configuration:

Archivematica Configuration
---------------------------

When installing Archivematica, options to configure it with the Storage
Service will be presented.

.. image:: images/Install3.*
   :align: center
   :width: 80%
   :alt: Configuring the Storage Service during Archivematica installation.

If you have installed the Storage Service at a different URL, you may change
that here.

The top button 'Use default transfer source & AIP storage locations' will
attempt to automatically configure default Locations for Archivematica,
register a new Pipeline, and generate an error if the Storage Service is not
available. Use this option if you want the Storage Service to automatically
set up the configured default values.

The bottom button 'Register this pipeline & set up transfer source and AIP
storage locations' will only attempt to register a new Pipeline with the
Storage Service, and will not error if not Storage Service can be found. It
will also open a link to the provided Storage Service URL, so that Locations
can be configured manually. Use this option if the default values not desired,
or the Storage Service is not running yet. Locations will have to be
configured manually before any Transfers can be processed, or AIPs stored.

If the Storage Service is running, the URL to it should be entered, and
Archivematica will attempt to register its dashboard UUID as a new Pipeline.
Otherwise, the dashboard UUID is displayed, and a Pipeline for this
Archivematica instance can be manually created and configured. The dashboard
UUID is also available in Archivematica under Administration -> General.

Change the port in the web server configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The storage services uses nginx by default, so you can edit
``/etc/nginx/sites-enabled/storage`` and change the line that says

``listen 8000;``

change ``8000`` to whatever port you prefer to use.

Keep in mind that in a default installation of Archivematica, the dashboard is
running in Apache on port 80. So it is not possible to make nginx run on port
80 on the same machine. If you install the storage service on its own server,
you can set it to use port 80.

Make sure to adjust the dashboard UUID in the Archivematica dashboard under
Administration -> General.

.. _spaces:

Spaces
------

.. image:: images/Spaces.*
   :align: center
   :width: 80%
   :alt: Storage Service spaces screen.

A storage Space contains all the information necessary to connect to the
physical storage. It is where protocol-specific information, like an NFS
export path and hostname, or the username of a system accessible only via SSH,
is stored. All locations must be contained in a space.

A space is usually the immediate parent of the Location folders. For example,
if you had transfer source locations at ``/home/artefactual/archivematica-
sampledata-2013-10-10-09-17-20`` and ``/home/artefactual/maildir_transfers``, the
Space's path would be ``/home/artefactual/``

Currently supported protocols are local filesystem, NFS, pipeline
local filesystem, LOCKSS, DuraCloud, Arkivum, Fedora and Swift.

Some protocols require a staging path. This is a temporary location on the
Storage Service server that is used when copying material from that service
service (DuraCloud, Swift, etc) to another space within the Storage Service.

.. _local-filesystem:

Local Filesystem
^^^^^^^^^^^^^^^^

Local Filesystem spaces handle storage that is available locally on the
machine running the storage service. Typically this is the hard drive, SSD or
raid array attached to the machine, but it could also encompass remote storage
that has already been mounted. For remote storage that has been locally
mounted, we recommend using a more specific Space if one is available.

**Fields**

* Path: Absolute path to the Space on the local filesystem

* Size: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

.. _nfs:

NFS
^^^

NFS spaces are for NFS exports mounted on the Storage Service server, and the
Archivematica pipeline.

**Fields**

* Path: Absolute path the space is mounted at on the filesystem local to the
  storage service

* Size: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Remote name: Hostname or IP address of the remote computer exporting the NFS
  mount.

* Remote path: Export path on the NFS server

* Version: nfs or nfs4 - as would be passed to the mount command.

* Manually Mounted: Check this if it has been mounted already. Otherwise, the
  Storage Service will try to mount it. Note: this feature is not yet available.


.. _pipeline:

Pipeline Local Filesystem
^^^^^^^^^^^^^^^^^^^^^^^^^

Pipeline Local Filesystems refer to the storage that is local to the
Archivematica pipeline, but remote to the storage service. For this Space to
work properly, passwordless SSH must be set up between the Storage Service
host and the Archivematica host.

For example, the storage service is hosted on storage_service_host and
Archivematica is running on archivematica1 . The transfer sources for
Archivematica are stored locally on archivematica1, but the storage service
needs access to them. The Space for that transfer source would be a Pipeline
Local Filesystem.

.. note::

   Passwordless SSH must be set up between the Storage Service host and the
   computer Archivematica is running on.

**Fields**

* Path: Absolute path to the space on the remote machine.

* Size: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Remote name: Hostname or IP address of the computer running Archivematica.
  Should be SSH accessible from the Storage Service computer.

* Remote user: Username on the remote host

.. _admin-lockss:

LOCKSS
^^^^^^

Archivematica can store AIPs in a `LOCKSS <http://www.lockss.org/>`_ network via
LOCKSS-O-Matic, which uses SWORD to communicate between the Storage Service
and a Private LOCKSS Network (PLN).

**Fields:**

* Size: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Path: Absolute path to the space on the remote machine.

* Staging path:  Absolute path to a staging area. Must be UNIX filesystem
  compatible, preferably on the same filesystem as the path.

* Service document IRI: URL of LOCKSS-o-matic service document IRI, eg.
  http://lockssomatic.example.org/api/sword/2.0/sd-iri

* Content Provider ID: On-Behalf-Of value when communicating with LOCKSS-o-matic

* Externally available domain: Base URL for this server that LOCKSS will be
  able to access. Generally this is the URL for the home page of the Storage
  Service.

* Keep local copy? Check the box if you wish to store a local copy of the AIPs
  even after they are stored in LOCKSS.

.. note::

   When creating a Location for a LOCKSS space (see below), the Purpose of the
   Location must be AIP Storage.

.. _duracloud:

DuraCloud
^^^^^^^^^

.. seealso::

   :ref:`Archivematica-DuraCloud Quick Start Guide <archivematica:duracloud-setup>`

Archivematica can use DuraCloud as an access protocol for the Storage Service in
version 0.5 and higher. Typically one Storage Service space has a one to one
relationship with a space within DuraCloud.

**Fields:**

* Size: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Path: Absolute path to the space on the remote machine. Normally left blank for
  DuraCloud implementations.

* Staging path:  Absolute path to a staging area. Must be UNIX filesystem
  compatible, preferably on the same filesystem as the path.

* Host: Hostname of the DuraCloud instance, e.g. example.duracloud.org

* User: Username to authenticate as

* Password: Password to authenticate with

* Duraspace: Name of the Space within DuraCloud

.. _arkivum:

Arkivum
^^^^^^^

Archivematica can use Arkivum's A-Stor as an access protocol in version 0.7 and
higher. A-Stor can expose a CIFS share to the Storage Service so that the
storage service can copy files to an A-Stor datapool for AIP storage, for example.

Add an entry to ``/etc/fstab`` on the Storage Service, then mount the A-Stor
CIFS share.

Example:

.. code:: bash

   //ARK00092/astor /mnt/astor cifs
   defaults,guest,file_mode=0666,dir_mode=0777,uid=archivematica,gid
   =archivematica,forcegid,forceuid,rw 0 1

In this example, ARK00092 is the name of the appliance and should be resolvable
through DNS or be set as an entry in ``/etc/hosts``.

Then, choosing Arkivum as the access protocol, create a new space in the
Storage Service:

**Fields**

* Size: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Path: local path on the Storage Service machine to the CIFS share.

Example: ``/mnt/astor``

* Staging Path: Absolute path to a staging area. Must be UNIX filesystem
  compatible, preferably on the same filesystem as the path.

Example: ``/mnt/astor/archivematica1/tmp``

* Host: Arkivum appliance hostname or IP address with port.

* Remote user: (Optional) Username on the remote machine accessible via
  passwordless ssh.

* Remote name: (Optional) Name or IP of the remote machine.

.. _swift:

Swift
^^^^^

OpenStack's Swift is available as an access protocol in Storage Service 0.7 and
higher. At this time, locations within Swift have been tested as AIP Storage,
DIP Storage and Transfer Backlog. Using Swift as Transfer Source is possible,
but under-tested at this time.

**Fields**

* Size (Optional): Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Path: Absolute path to the space on the storage service machine.

* Staging Path: Absolute path to a staging area. Must be UNIX filesystem
  compatible, preferably on the same filesystem as the path.

* Auth url: URL to authenticate against

* Auth version: OpenStack auth version

* Username: Username to authenticate as (Swift username)

* Password: Password to authenticate with (Swift password)

* Container: Name of the Swift container. To list available containers in
  your Swift installation, run ``swift list`` from the command line.

* Tenant: The tenant/account name, required when connecting to an auth
  2.0 system.

* Region (Optional): Region in Swift.

.. _fedora:

Fedora via SWORD2
^^^^^^^^^^^^^^^^^

Fedora via SWORD2 is currently supported in the Storage Service as an Access
Protocol to facilitate use of the
`Archidora plugin <https://wiki.duraspace.org/display/ISLANDORA/Archidora>`_,
which allows ingest of material from Islandora to Archivematica. This workflow
is in beta testing as of Storage Service 0.8/Archivematica 1.5/Islandora 7.x-1.6.

**Fields**

* Size (Optional): Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.

* Path: Absolute path to the space on the storage service machine.
  E.g. '/var/archivematica/storage_service/archidora'

* Staging Path: Absolute path to a staging area. Must be UNIX filesystem
  compatible, preferably on the same filesystem as the path.
  E.g. '/var/archivematica/storage_service/archidora_staging'

* Fedora user: Fedora user name (for SWORD functionality)

* Fedora password: Fedora password (for SWORD functionality)

* Fedora name: Name or IP of the remote Fedora machine

.. note::

   * A Location (see below) must also be created, with the purpose 
     of FEDORA Deposits.

   * To allow transfers to be approved automatically:
   
     * Ensure that the pipeline has been configured with the correct
       username and API key for the pipeline (Archivematica dashboard) user
     * On the Archivematica dashboard, the IP of the storage service. 
       needs to be added to the IP whitelist for the REST API

   * A post-store callback can be configured, to enable Islandora to 
     list objects that can be deleted once they have been
     stored by Archivematica. See the :ref:`Administration <administration>` section.

.. _locations:

Locations
---------

.. image:: images/Locations.*
   :align: center
   :width: 80%
   :alt: Storage Service locations screen.

A storage Location is contained in a Space, and knows its purpose in the Archivematica system.
Each Location is associated with at least one pipeline; with the exception of Backlog and Currently Processing locations, for which there must be exactly one per pipeline, a pipeline can have multiple instances of any location, and a location can be associated with any number of pipelines.
Currently, a Location can have one of eight purposes: Transfer Source, Transfer Backlog, AIP Storage, DIP Storage, Currently Processing, Storage Service Internal Processing, AIP Recovery or FEDORA Deposit.

Transfer source locations display in Archivematica's Transfer tab, and any folder in a transfer source can be selected to become a Transfer.
The default value is '/home' in a Local Filesystem.
This is required to start transfers.

Transfer backlog stores transfers until such a time that the user continues processing them.
The default value is '/var/archivematica/sharedDirectory/www/AIPsStore/transferBacklog' in a Local Filesystem.
This is required to store and retrieve transfers in backlog.

AIP storage locations are where the completed AIPs are put for long-term storage.
The default value is '/var/archivematica/sharedDirectory/www/AIPsStore' in a Local Filesystem.
This is required to store and retrieve AIPs.

Likewise, DIP storage is used for storing DIPs until such a time that they can be uploaded to an access system.
The default value is '/var/archivematica/sharedDirectory/www/DIPsStore' in a Local Filesystem.
This is required to store and retrieve DIPs.
This is not required to upload DIPs to access systems.

During processing, Archivematica uses the currently processing location associated with that pipeline.
Exactly one currently processing location should be associated with a given pipeline.
The default value is '/var/archivematica/sharedDirectory' in a Local Filesystem.
This is required for Archivematica to run.

Likewise, there should only be exactly one Storage Service Internal Processing location for each Storage Service installation.
The default value is '/var/archivematica/storage_service' in a Local Filesystem.
This is required for the Storage Service to run, and must be locally available to the storage service.
It should not be associated with any pipelines.

AIP Recovery is where the :ref:`AIP recovery <recovery>` feature looks for an AIP to recover.
No more than one AIP recovery locatio should be associated with a given pipeline.
The default value is '/var/archivematica/storage_service/recover' in a Local Filesystem.
This is only required if AIP recovery is used.

FEDORA Deposit is used with the Archidora plugin to ingest material from Islandora.
This is only available to the FEDORA Space, and is only required for that space.

If you want the same directory on disk to have multiple purposes, multiple Locations with different purposes can be created.

**Fields**

* Purpose: What use the Location is for
* Pipeline: Which pipelines this location is available to.
* Relative Path: Path to this Location, relative to the space that contains it.
* Description: Description of the Location to be displayed to the user.
* Quota: (Optional) Maximum size allowed for this space. Set to 0 or leave blank
  for unlimited.
* Enabled: If checked, this location is accessible to pipelines associated with
  it. If unchecked, it will not show up to any pipeline.

.. _pipelines:

Pipelines
---------

.. image:: images/Pipelines.*
   :align: center
   :width: 80%
   :alt: Storage Service pipelines screen.

A pipeline is an Archivematica instance registered with the Storage Service,
including the server and all associated clients. Each pipeline is uniquely
identified by a UUID, which can be found in the dashboard under Administration
-> General Configuration. When installing Archivematica, it will attempt to
register its UUID with the Storage Service, with a description of
"Archivematica on <hostname>".

**Fields**

* UUID: Unique identifier of the Archivematica pipeline
* Description: Description of the pipeline displayed to the user. e.g. Sankofa
  demo site
* Remote name: Hostname or IP of the pipeline
* Api username: Pipeline (dashboard) user to use when making API calls to the pipeline
* Api key: API key for the user indicated above
* Enabled: If checked, this pipeline can access locations associate with it.
  If unchecked, all locations will be disabled, even if associated.
* Default Locations: If checked, the default locations configured in
  Administration -> Configuration will be created or associated with the new
  pipeline.

.. _packages:

Packages
--------

.. image:: images/Packages.*
   :align: center
   :width: 80%
   :alt: Storage Service packages screen.

A Package is a file that Archivematica has stored in the Storage Service,
commonly an Archival Information Package (AIP). They cannot be created or
deleted through the Storage Service interface, though a deletion request can
be submitted through Archivematica that must be approved or rejected by the
storage service administrator. To learn more about deleting an AIP, see
:ref:`Deleting an AIP <archivematica:delete-aip>`.

.. _administration:

Administration
--------------

.. image:: images/StorageserviceAdmin1.*
   :align: center
   :width: 80%
   :alt: Storage Service Administration screen part 1.

.. image:: images/StorageserviceAdmin2.*
   :align: center
   :width: 80%
   :alt: Storage Service Administration screen part 2.

.. image:: images/StorageserviceAdmin3.*
   :align: center
   :width: 80%
   :alt: Storage Service Administration screen part 3.

The Administration section manages the users and settings for the Storage
Service.

Users
^^^^^

Only registered users can long into the storage service, and the Users page is
where users can be created or modified.

The storage service has two types of users: administrative users, and regular
users. The only distinction between the two types is for email notifications;
administrators will be notified by email when special events occur, while
regular users will not.

Settings
^^^^^^^^

Settings control the behavior of the Storage Service. Default Locations are
the created or associated with pipelines when they are created.

**Pipelines are disabled upon creation?** sets whether a newly created Pipeline
can access its Locations. If a Pipeline is disabled, it cannot access any of
its locations. By disabling newly created Pipelines, it provides some security
against unwanted perusal of the files in Locations, or use by unauthorized
Archivematica instances. This can be configured individually when creating a
Pipeline manually through the Storage Service website.

**Default Locations** sets which existing locations should be associated with a
newly created Pipeline, or which new Locations should be created for each new
Pipeline. No matter what is configured here, a Currently Processing location
is created for all Pipelines, since one is required. Multiple Transfer Source
or AIP Storage Locations can be configured by holding down Ctrl when selecting
them. New Locations in an existing Space can be created for Pipelines that use
default locations by entering the relevant information.

Service Callbacks
^^^^^^^^^^^^^^^^^

Callbacks allow REST calls to be made by the Archivematica Storage Service
after performing certain types of actions. This allows external services
to be notified when internal actions have taken place.

A callback can be configured for the Islandora (Fedora) integration, as follows:

* URI: http://{islandora-base-url}/islandora/object/<source_id>/archidora/{Islandora API key}/delete
  (the Islandora API key is generated on the Archidora admin screen in Islandora)
* Event: post-store
* Method: post
* Expected status: 200

How to Configure a Location
^^^^^^^^^^^^^^^^^^^^^^^^^^^

For Spaces of the type "Local Filesystem," Locations are basically directories
(or more accurately, paths to directories). You can create Locations for
Transfer Source, Currently Processing, or AIP and DIP Storage.

To create and configure a new Location:

1. In the Storage Service, click on the "Spaces" tab.
2. Under the Space that you want to add the Location to, click on the
   "Create Location here" link.
3. Choose a purpose (e.g. AIP Storage) and pipeline, and enter a "Relative Path"
   (e.g. ``var/mylocation``) and human-readable description. The Relative Path is
   relative to the Path defined in the Space you are adding the Location to.
   For example, for the default Space, the Path is ``/`` so your Location path
   would be relative to that (in the example here, the complete path would end
   up being ``/var/mylocation``).

..  note::

    If the path you are defining in your Location  doesn't exist, you must
    create it manually and make sure it is writable by the Archivematica
    user.

4. Save the Location settings.
5. The new Location will now be available as an option under the appropriate
   options in the Dashboard, for example as a Transfer location (which must be
   enabled under the Dashboard "Administration" tab) or as a destination for AIP
   storage.

:ref:`Back to the top <administrators>`
