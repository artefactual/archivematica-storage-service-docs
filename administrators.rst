.. _administrators:

==========================================
Storage Service Administrator Instructions
==========================================

The Archivematica Storage Service allows the configuration of storage spaces
associated with multiple Archivematica pipelines. It allows a storage
administrator to configure what storage is available to each Archivematica
installation, both locally and remote.

.. image:: images/SS1-0.*
   :align: center
   :width: 80%
   :alt: Home page of the Storage Service


*On this page:*

* :ref:`Storage Service glossary and organization <organization>`

* :ref:`Archivematica Configuration <archivematica-configuration>`

* :ref:`Pipelines <pipelines>`

* :ref:`Spaces <spaces>`

  * :ref:`Arkivum <arkivum>`
  * :ref:`Dataverse <dataverse>`
  * :ref:`DuraCloud <duracloud>`
  * :ref:`FEDORA via SWORD2 <fedora>`
  * :ref:`GPG encyption on local filesystem <gpg>`
  * :ref:`Local filesystem <local-filesystem>`
  * :ref:`LOCKSS (LOCKSS-o-matic) <admin-lockss>`
  * :ref:`NFS <nfs>`
  * :ref:`Pipeline local filesystem <pipeline>`
  * :ref:`Swift <swift>`
  * :ref:`S3 (Amazon) <amazon-s3>`

* :ref:`Locations <locations>`

* :ref:`Packages <packages>`

* :ref:`Administration <administration>`

.. _organization:

Storage Service glossary and organization
-----------------------------------------

Pipelines
^^^^^^^^^

A pipeline refers to a single installation of an Archivematica dashboard. One
Storage Service can be used to configure multiple Archivematica pipelines.

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
   :width: 60%
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

The Storage Services uses nginx by default, so you can edit
``/etc/nginx/sites-enabled/storage`` and change the line that says

``listen 8000;``

change ``8000`` to whatever port you prefer to use.

Keep in mind that in a default installation of Archivematica, the dashboard is
running in Apache on port 80. So it is not possible to make nginx run on port
80 on the same machine. If you install the Storage Service on its own server,
you can set it to use port 80.

Make sure to adjust the dashboard UUID in the Archivematica dashboard under
Administration -> General.

.. _pipelines:

Pipelines
---------

.. image:: images/Pipelines.*
   :align: center
   :width: 80%
   :alt: Storage Service pipelines screen.

A pipeline is an Archivematica instance registered with the Storage Service,
including the server and all associated clients. Each pipeline is uniquely
identified by a UUID, which can be found in the dashboard under
**Administration** -> **General Configuration**. When installing Archivematica,
it will attempt to register the UUID with the Storage Service, with a
description of "Archivematica on <hostname>".

Fields:

* **UUID**: the unique identifier of the Archivematica pipeline.
* **Description**: a description of the pipeline displayed to the user. e.g.
  ``Development site``.
* **Enabled**: If checked, this pipeline can access locations associate with it.
  If unchecked, all locations will be disabled, even if associated directly with
  this pipeline.
* **Default Location**: If checked, the default locations configured in
  Administration -> Configuration will be created or associated with the new
  pipeline.


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

.. _arkivum:

Arkivum
^^^^^^^

Archivematica can use Arkivum's A-Stor as an access protocol in version 0.7 and
higher. A-Stor can expose a CIFS share to the Storage Service so that the
Storage Service can copy files to an A-Stor datapool for AIP storage, for
example.

Add an entry to ``/etc/fstab`` on the Storage Service, then mount the A-Stor
CIFS share.

Example::

   //ARK00092/astor /mnt/astor cifs
   defaults,guest,file_mode=0666,dir_mode=0777,uid=archivematica,gid
   =archivematica,forcegid,forceuid,rw 0 1

In this example, ARK00092 is the name of the appliance and should be resolvable
through DNS or be set as an entry in ``/etc/hosts``.

Then, choosing Arkivum as the access protocol, create a new space in the
Storage Service.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the local path on the Storage Service machine to the CIFS share,
  e.g. ``/mnt/astor``
* **Staging Path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible, preferably on the same filesystem as the path, e.g.
  ``/mnt/astor/archivematica1/tmp``
* **Host**: the hostname of the Arkivum web instance or IP address with port,
  e.g. ``arkivum.example.com:8443``
* **Remote user**: the username on the remote machine accessible via
  passwordless ssh. This field is optional.
* **Remote name**: the name or IP of the remote machine. This field is optional.

.. _dataverse:

Dataverse
^^^^^^^^^

Dataverse Integration is supported with Archivematica v1.8+ and Storage Service v0.13+

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Host**: Hostname or IP address of the Dataverse instance, e.g.
  ``test.dataverse.org``
* **API key**: the key generated by Dataverse for a specific user account
* **Agent name**: a string that will be used in Archivematica METS file to
  identify this Dataverse instance as a PREMIS agent, e.g. ``My Institution's
  Dataverse Instance``. This field is optional.
* **Agent type**: a string that will be used in Archivematica METS file
  to identify the type of PREMIS agent named above, e.g. ``organization``. This
  field is optional.
* **Agent identifier**: a string that will be used as an identifier in the
  Archivematica METS file to uniquely identify the PREMIS agent named above.
  This field is optional.

Inside this space, at least one location should be created for the purpose of
Transfer Source. This is the only type of location that will be allowed inside a
Dataverse space.

Within this location, the relative path can be used to store a query string,
which is used to interrogate the Dataverse Search API. The q parameter is a 
general search parameter. The ’subtree’ parameter can be used to indicate a 
sub-dataverse. For example:
   q=*
   subtree=“Archivematica”
will return all datasets contained within the “Archivematica” sub-dataverse. 

The Storage Service will always add ``type=dataset`` to the calls that it makes
to the Dataverse Search API. 

For futher details of the API parameters, see the `Dataverse api guide`_

.. _duracloud:

DuraCloud
^^^^^^^^^

.. seealso::

   :ref:`Archivematica-DuraCloud Quick Start Guide <archivematica:duracloud-setup>`

Archivematica can use DuraCloud as an access protocol for the Storage Service in
version 0.5 and higher. Typically, a Storage Service space has a one-to-one
relationship with a space within DuraCloud.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Host**: the hostname of the DuraCloud instance, e.g.
  ``example.duracloud.org``.
* **User**: a username with sufficient permissions to permit authentication.
* **Password**: the password of the above user.
* **Duraspace**: the name of the Space within DuraCloud

.. _dspace:

DSpace via SWORD2 API
^^^^^^^^^^^^^^^^^^^^^

DSpace locations are currently supported only for AIP Storage locations.  Becasue
DSpace is typically used as a public-facing system, the behaviour is different
than when using other access protocols for AIP Storage: upon deposit in DSpace,
the AIP will be split into an objects bitstream, which contains all original and
normalized objects in the AIP, as well as a metadata bitstream, which contains
all of the bag artifacts, metadata and logs that one would expect to see in
an Archivematica AIP. The metadata bitstream can optionally be restricted; see
below. Presently, the Storage Service and Dashboard are not capable of
downloading/reconstituting the AIP- this must be done manually from
the DSpace interface.

If using DSpace as the AIP location in conjunction with the ArchivesSpace workflow
in the :ref:`Appraisal tab <archivematica:appraisal>`, a post Store AIP hook will
send the DSpace handle to the ArchivesSpace digital object record upon AIP
storage.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Service Document IRI**: URL of the service document. E.g.
  ``http://demo.dspace.org/swordv2/servicedocument``, where servicedocument is
  the handle to the community or collection being used for deposit.
* **User**: a username for the DSpace instance with sufficient permissions to
  permit authentication.
* **Password**: the password for the username above.
* **Restricted metadata policy**: Use to restrict access to the metadata
  bitstream. Must be specified as a list of objects in JSON, e.g.
  ``[{"action":"READ","groupId":"5","rpType":"TYPE_CUSTOM"}]``. This will
  override existing policies.

.. _fedora:

Fedora via SWORD2
^^^^^^^^^^^^^^^^^

Fedora via SWORD2 is currently supported in the Storage Service as an Access
Protocol to facilitate use of the
`Archidora plugin <https://wiki.duraspace.org/display/ISLANDORA/Archidora>`_,
which allows ingest of material from Islandora to Archivematica. This workflow
is in beta testing as of Storage Service 0.9/Archivematica 1.5/Islandora 7.x-1.6.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Fedora user**: Fedora user name (for SWORD functionality).
* **Fedora password**: Fedora password (for SWORD functionality).
* **Fedora name**: Name or IP of the remote Fedora machine.

.. note::

   * A Location (see below) must also be created, with the purpose
     of FEDORA Deposits.

   * On the Archivematica dashboard, the IP of the Storage Service
     needs to be added to the IP whitelist for the REST API, so that
     transfers will be approved automatically.

   * A post-store callback can be configured, to enable Islandora to
     list objects that can be deleted once they have been
     stored by Archivematica. See the :ref:`Administration <administration>` section.

.. _gpg:

GPG encryption on local file system
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Creating a GPG encryption space will allow users to create encrypted AIPs and
transfers. Only AIP storage, Transfer backlog and Replicator locations can be
created in a GPG encryption space.

Encrypted AIPs and transfers can be downloaded unencrypted via the Storage
Service and Archivematica dashboard.

Before creating a GPG encryption space ensure that you have created or imported
a GPG key on the :ref:`Administration page <administration>`.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Key**: the encryption key to be used for the space.

.. important::

   It is possible to encrypt uncompressed AIPs, which will be stored as tar
   files.

   Uncompressed AIPs do not have pointer files, so if the key for the space
   is changed and the original key is deleted/unknown, Archivematica will
   have no record of the key for decryption.

.. _local-filesystem:

Local Filesystem
^^^^^^^^^^^^^^^^

Local Filesystem spaces handle storage that is available locally on the
machine running the Storage Service. Typically this is the hard drive, SSD or
raid array attached to the machine, but it could also encompass remote storage
that has already been mounted. For remote storage that has been locally
mounted, we recommend using a more specific Space if one is available.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.


.. _admin-lockss:

LOCKSS
^^^^^^

Archivematica can store AIPs in a `LOCKSS`_ network via LOCKSS-O-Matic, which
uses SWORD to communicate between the Storage Service and a Private LOCKSS
Network (PLN).

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Service document IRI**: the URL of the LOCKSS-o-matic service document IRI,
  e.g. ``http://lockssomatic.example.org/api/sword/2.0/sd-iri``.
* **Content Provider ID**: the On-Behalf-Of value when communicating with
  LOCKSS-o-matic.
* **Externally available domain**: the base URL for this server that LOCKSS will
  be able to access. Generally this is the URL for the home page of the Storage
  Service.
* **Keep local copy?**: check this box if you wish to store a local copy of the
  AIPs even after they are stored in LOCKSS.

.. note::

   When creating a Location for a LOCKSS space (see below), the Purpose of the
   Location must be AIP Storage.

.. _nfs:

NFS
^^^

NFS spaces are for NFS exports mounted on the Storage Service server and the
Archivematica pipeline.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to where the space is mounted on the filesystem
  local to the Storage Service.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Remote name**: the hostname or IP address of the remote computer exporting
  the NFS mount.
* **Remote path**: the export path on the NFS server
* **Version**:  the version of the filesystem, e.g. ``nfs`` or ``nfs4``,as would
  be passed to the mount command.
* **Manually mounted**: This is a placeholder for a feature that is not yet
  available.

.. _pipeline:

Pipeline Local Filesystem
^^^^^^^^^^^^^^^^^^^^^^^^^

Pipeline Local Filesystems refer to the storage that is local to the
Archivematica pipeline, but remote to the Storage Service. For this Space to
work properly, passwordless SSH must be set up between the Storage Service
host and the Archivematica host.

For example, the Storage Service is hosted on storage_service_host and
Archivematica is running on archivematica1 . The transfer sources for
Archivematica are stored locally on archivematica1, but the Storage Service
needs access to them. The Space for that transfer source would be a Pipeline
Local Filesystem.

.. note::

   Passwordless SSH must be set up between the Storage Service host and the
   computer Archivematica is running on.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to where the space is mounted on the filesystem
  local to the Storage Service.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Remote user**: the username on the remote host.
* **Remote name**: the hostname or IP address of the computer running
  Archivematica. This should be SSH accessible from the Storage Service
  computer.
* **Assume remote host serving files with rsync daemon**: if checked, the
  Storage Service will use rsync daemon-style commands instead of the default
  rsync with remote shell.
* **Rsync password**: the password for the rsync daemon

.. _swift:

Swift
^^^^^

OpenStack's Swift is available as an access protocol in Storage Service 0.7 and
higher. At this time, locations within Swift have been tested as AIP Storage,
DIP Storage and Transfer Backlog. Using Swift as Transfer Source is possible,
but under-tested at this time.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **Auth URL**: the URL to authenticate against.
* **Auth version**: the OpenStack authentication version.
* **Username**: the Swift username that will be used for authentication.
* **Password**: the password for the above username.
* **Container**: the name of the Swift container. To list available containers
  in your Swift installation, run ``swift list`` from the command line.
* **Tenant**: the tenant/account name, required when connecting to an auth
  2.0 system.
* **Region**: the region in Swift. This field is optional.

.. _amazon-s3:

S3 (Amazon)
^^^^^^^^^^^

Amazon S3 is available as an access protocol as of Storage Service 0.12. At this
time, locations within S3 have only been tested for AIP Storage.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **S3 Endpoint URL**: the URL of the S3 endpoint, e.g.
  ``https://s3.amazonaws.com``.
* **Access Key ID to authenticate**: the public key generated by S3.
* **Secret Access Key to authenticate with**: the secret key generated by S3.
* **Region**: the region that the S3 instance uses, e.g. ``us-east-2``.

.. _locations:

Locations
---------

.. image:: images/Locations.*
   :align: center
   :width: 80%
   :alt: Storage Service locations screen.

A storage Location is contained within a Space, and knows its purpose in the
Archivematica system. Each Location is associated with at least one pipeline;
with the exception of Backlog and Currently Processing locations, for which
there must be exactly one per pipeline, a pipeline can have multiple instances
of any location, and a location can be associated with any number of pipelines.
Currently, a Location can have one of eight purposes: Transfer Source, Transfer
Backlog, AIP Storage, DIP Storage, Currently Processing, Storage Service
Internal Processing, AIP Recovery or FEDORA Deposit.

Transfer source locations display in Archivematica's Transfer tab, and any
folder in a transfer source can be selected to become a Transfer. The default
value is ``/home`` in a Local Filesystem. This is required to start transfers.

Transfer backlog stores transfers until such a time that the user continues
processing them. The default value is
``/var/archivematica/sharedDirectory/www/AIPsStore/transferBacklog`` in a Local
Filesystem. This is required to store and retrieve transfers in backlog.

AIP storage locations are where the completed AIPs are put for long-term
storage. The default value is '/var/archivematica/sharedDirectory/www/AIPsStore'
in a Local Filesystem. This is required to store and retrieve AIPs.

Likewise, DIP storage is used for storing DIPs until such a time that they can
be uploaded to an access system. The default value is
``/var/archivematica/sharedDirectory/www/DIPsStore`` in a Local Filesystem.
This is required to store and retrieve DIPs. This is not required to upload DIPs
to access systems.

During processing, Archivematica uses the currently processing location
associated with that pipeline. Exactly one currently processing location should
be associated with a given pipeline. The default value is
``/var/archivematica/sharedDirectory`` in a Local Filesystem. This is required
for Archivematica to run.

Likewise, there should only be exactly one Storage Service Internal Processing
location for each Storage Service installation. The default value is
``/var/archivematica/storage_service`` in a Local Filesystem. This is required
for the Storage Service to run, and must be locally available to the Storage
Service. It should not be associated with any pipelines.

AIP Recovery is where the :ref:`AIP recovery <recovery>` feature looks for an
AIP to recover. No more than one AIP recovery locatio should be associated with
a given pipeline. The default value is
``/var/archivematica/storage_service/recover`` in a Local Filesystem. This is
only required if AIP recovery is used.

FEDORA Deposit is used with the Archidora plugin to ingest material from
Islandora. This is only available to the FEDORA Space, and is only required for
that space.

Replicator locations can be configured to replicate the AIPs in one or more AIP
storage locations. If you wish for the replicated AIPs to be encrypted, create
the location in an :ref:`encrypted space <gpg>`.

If you want the same directory on disk to have multiple purposes, multiple
Locations with different purposes can be created.

Fields:

* **Purpose**: the function that this location will fulfill, e.g.
  ``AIP storage``.
* **Pipelines**: the Archivematica instance(s) that will be able to use this
  location.
* **Relative Path**: the path to this location, relative to the space that
  contains it.
* **Description**: a description of the location to be displayed to the user.
* **Quota**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Enabled**: if checked, this location will be accessible to pipelines
  associated with it. If unchecked, it will not be available to any pipeline.
* **Set as global default location for its purpose**: if checked, this location
  will be the default location for its purpose unless the user specifically
  tells Archivematica otherwise during processing.

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

4. If desired, for an AIP storage location choose the replicator location(s).
5. Save the Location settings.
6. The new Location will now be available as an option under the appropriate
   options in the Dashboard, for example as a Transfer location (which must be
   enabled under the Dashboard "Administration" tab) or as a destination for AIP
   storage.

.. _packages:

Packages
--------

.. image:: images/Packages.*
   :align: center
   :width: 80%
   :alt: Storage Service packages screen.

A Package is a file that Archivematica has stored in the Storage Service,
commonly an Archival Information Package (AIP). Dissemination Information Packages
(DIPs) which have been stored and Transfers which have been sent to backlog will
also be reflected in the Packages tab.

AIPs cannot be created or deleted through the Storage Service interface, though a
deletion request can be submitted through Archivematica that must be approved or
rejected by the Storage Service administrator. To learn more about deleting an AIP, see
:ref:`Deleting an AIP <archivematica:delete-aip>`. Stored DIPs cannot be deleted
through either the Storage Service or Archivematica interfaces. Deletion requests
for transfers are automatically generated when all of the objects from the transfer
have successfully been stored in AIPs.

For more information about Fixity Status, see :ref:`Fixity <fixity>`.


.. _administration:

Administration
--------------

The Administration section manages the users and settings for the Storage
Service.

.. image:: images/StorageserviceAdmin.*
   :align: center
   :width: 80%
   :alt: Storage Service Administration screen.

Users
^^^^^

Only registered users can long into the Storage Service, and the Users page is
where users can be created or modified.

The Storage Service has two types of users: administrative users, and regular
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

Version
^^^^^^^

The version page will display the current version and specific git commit of your installation of the Storage Service.

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

Encryption Keys
^^^^^^^^^^^^^^^

GPG encryption keys can be created or imported to be used in spaces to store
encrypted AIPs, transfers or replicated AIPs/transfers. Keys can either be
created by the Storage Service or imported.

To create a new key:

1. Click on Create New Key

2. Enter the name and email address you want associated with the key.

To import a key:

1. Click on Import Existing Key

2. Paste in your key in ASCII-armored format.

Set Language
^^^^^^^^^^^^^^^

Configure language settings for the Storage Service in this area of the Administration tab. Strings are available for translation on the localization platform (Transifex).

:ref:`Back to the top <administrators>`

.. _`LOCKSS`: http://www.lockss.org/
.. _`Dataverse api guide`: http://guides.dataverse.org/en/latest/api/search.html
