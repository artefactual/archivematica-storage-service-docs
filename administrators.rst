.. _administrators:

=================================
Administering the Storage Service
=================================

The Archivematica Storage Service allows the configuration of storage spaces
associated with multiple Archivematica pipelines. It allows a storage
administrator to configure what storage is available to each Archivematica
installation, both locally and remote.

*On this page*

* :ref:`Storage Service glossary and organization <organization>`
* :ref:`Archivematica configuration <archivematica-configuration>`
* :ref:`Pipelines <pipelines>`
* :ref:`Spaces <spaces>`

  * :ref:`Access protocols <access-protocols>`

* :ref:`Locations <locations>`

  * :ref:`Location purposes <location-purposes>`

* :ref:`Packages <packages-tab>`
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

Archivematica configuration
---------------------------

When installing Archivematica, options to configure it with the Storage
Service will be presented.

.. image:: images/Install3.*
   :align: center
   :width: 40%
   :alt: Configuring the Storage Service during Archivematica installation.

If you have installed the Storage Service at a different URL, you should change
that here.

The **Use default transfer source & AIP storage locations** option will attempt to
automatically configure default Locations for Archivematica, register a new
Pipeline, and generate an error if the Storage Service is not available. Use
this option if you want the Storage Service to automatically set up the
configured default values.

The **Register this pipeline & set up transfer source and AIP storage
locations** option will only attempt to register a new Pipeline with the Storage
Service, and will not error if not Storage Service can be found. It will also
open a link to the provided Storage Service URL, so that Locations can be
configured manually. Use this option if the default values not desired, or the
Storage Service is not running yet. Locations will have to be configured
manually before any Transfers can be processed, or AIPs stored.

If the Storage Service is running, the URL to it should be provided and
Archivematica will attempt to register the dashboard UUID as a new Pipeline.
Otherwise, the dashboard UUID is displayed and a Pipeline for the
Archivematica instance can be manually created and configured. The dashboard
UUID is also available in Archivematica under Administration -> General.

Change the port in the web server configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Storage Services uses nginx by default. To change the port, edit the file
``/etc/nginx/sites-enabled/storage`` and change the line that says ``listen
8000;``, replacing 8000 with whatever port you use.

Keep in mind that in a default installation of Archivematica, the dashboard is
running in Apache on port 80. It is not possible to make nginx run on port 80 on
the same machine. If you install the Storage Service on its own server, you can
set it to use port 80.

Make sure to adjust the dashboard UUID in the Archivematica dashboard under
Administration -> General.

.. _pipelines:

Pipelines
---------

The pipeline in Archivematica is the Archivematica dashboard. All pipelines need
to be registered with the Storage Service using the pipeline's unique universal
identifier (UUID). The UUID can be found in the Archivematica dashboard under
Administration -> General Configuration. When you first install Archivematica,
it will attempt to register the pipeline's UUID automatically with the Storage
Service with the description ``Archivematica on <hostname>``.

.. image:: images/Pipelines.*
   :align: center
   :width: 80%
   :alt: Storage Service pipelines screen.

A single Storage Service can be connected to many pipelines. To connect a
pipeline to an existing Storage Service, click on **Create new pipeline** on the
Pipelines tab of the Storage Service.

Fields:

* **UUID**: The unique identifier of the Archivematica pipeline.
* **Description**: A description of the pipeline that will be displayed to the
  user. e.g. ``Development site``.
* **Remote name**: the base URL of the pipeline server. This is used for making
  API calls.
* **API username**: The username to use when making API calls to the pipeline.
* **API key**: The API key to use when making API calls to the pipeline.
* **Enabled**: If checked, this pipeline can access locations associate with it.
  If unchecked, all locations associated with this pipeline will be disabled.
* **Default Location**: If checked, the default locations that have been
  selected in Administration -> Configuration will be created for or associated
  with the new pipeline.

.. _spaces:

Spaces
------

Spaces contain all of the information needed to connect Archivematica to a
storage location. The space is where protocol-specific information, like an NFS
export path and hostname, or the username of a system accessible only via SSH,
is stored.

.. image:: images/Spaces.*
   :align: center
   :width: 80%
   :alt: Storage Service spaces screen.

Each space has specific configuration fields depending on the access protocol
that is selected. See :ref:`Access protocols <access-protocols>` below for
more information.

.. note::

   A space is usually the immediate parent of the Location folders. For example,
   if you want to define two transfer source locations located at
   ``/home/artefactual/archivematica-sampledata`` and
   ``/home/artefactual/maildir_transfers``, the space's path would be
   ``/home/artefactual/``.

.. _access-protocols:

Access protocols
^^^^^^^^^^^^^^^^

.. _arkivum:

Arkivum
+++++++

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
+++++++++

Dataverse Integration is supported with Archivematica v1.8 (and higher) and
Storage Service v0.13 (and higher).

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
  Dataverse Instance``.
* **Agent type**: a string that will be used in Archivematica METS file
  to identify the type of PREMIS agent named above, e.g. ``organization``.
* **Agent identifier**: a string that will be used as an identifier in the
  Archivematica METS file to uniquely identify the PREMIS agent named above.

Dataverse spaces support Transfer Source Locations (Locations for other
purposes are not currently supported). At least one location should be created
as a Transfer Source.

Within this location, the relative path can be used to set two of the parameters
available in the Dataverse Search API. The q (or "Query") parameter is a
general search parameter. The ’subtree’ parameter can be used to indicate a
sub-dataverse. For example, the following entry in 'Relative Path'::

  Query:*
  Subtree:Archivematica

will return all datasets within the Archivematica dataverse. The other API
parameters are set using the fields described above for the space, or are set
with fixed values. The parameters used are::

  URL: https://<Host field set in Space configuration>/api/search/

  {
    'q': '<Relative Path field set in Location configuration>',
    'sort': 'name',
    'key': u '<API Key field set in Space configuration>',
    'start': 50,
    'per_page': 50,
    'show_entity_ids': True,
    'type': 'dataset',
    'subtree': '<Relative Path field set in Location configuration>',
    'order': 'asc'
  }

Search results are currently limited to 50 datasets. For repositories with more
than 50 datasets we recommend creating multiple Locations with more specific
search criteria. For further details of the API parameters, see the
`Dataverse api guide`_.

.. _duracloud:

DuraCloud
+++++++++

Archivematica can use DuraCloud as an access protocol for the Storage Service in
version 0.5 and higher. A Storage Service space has a one-to-one relationship
to a space within DuraCloud.

To set up your Archivematica instance with DuraCloud, please see :ref:`Using
DuraCloud with Archivematica <archivematica:duracloud-setup>`.

Fields:

* **Access protocol**: DuraCloud
* **Size**: *This field is optional.*
* **Path**: Leave this field blank.
* **Staging path**: A location on your local disk where Archivematica can
  place files for staging purposes, for example
  ``var/archivematica/storage_service/duracloud_staging``
* **Host**: Hostname of the DuraCloud instance, e.g. ``site.duracloud.org``
* **Username**: The username for the Archivematica user that you created in
  DuraCloud.
* **Password**: The password for the Archivematica user that you created in
  DuraCloud.
* **Duraspace**: The name of the space in DuraCloud you are creating this
  Storage Service space for (e.g. transfer-source, aip-store, etc).

.. _dspacerest:

DSpace via REST API
+++++++++++++++++++

DSpace via REST API locations are supported for both AIP and DIP Storage
locations. Because DSpace is typically used as a public-facing system, the
behaviour is different than when using other access protocols for AIP Storage.
Upon deposit in DSpace, the AIP will be deposited as a single compressed objects
file (bitstream), which contains all original and normalized objects as well as
the metadata pertaining to them. The DIP will be deposited as uncompressed files
with the folder structure flattened.

Presently, the Storage Service and Dashboard are not capable of
downloading/reconstituting the AIP - this must be done manually from the DSpace
interface.

The DSpace via REST API space offers a novel way to facilitate increased
automation. It is possible to supply :ref:`metadata with the transfer
<archivematica:import-metadata>` as either a CSV or a JSON file. In the entry
for the root folder ``objects`` it is possible to define any number of Dublin
Core Metadata Elements Set properties which will map on to the corresponding
DSpace record created for the AIP/DIP. In addition there are three optional
custom properties which can be defined:

* ``dspace_dip_collection``: the UUID of the DSpace collection into which the
  DIP will be deposited. E.g. ``a12d749c-7727-4121-b6be-478cacde658f``
* ``dspace_aip_collection``: the UUID of the DSpace collection into which the
  AIP will be deposited. E.g. ``80c3519d-7a07-4830-beae-a868c149ecbe``
* ``archivesspace_dip_collection``: the identifier of the archival object for
  which a child digital object will be created which will link to the DSpace DIP
  record. E.g. ``135569``

.. note::

   Note that the DSpace via REST API space only supports DSpace 6.x and not
   other versions of DSpace.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank for
  unlimited. This field is optional.
* **Path**: the absolute path to the Space on the local filesystem.
* **Staging path**: the absolute path to a staging area. Must be UNIX filesystem
  compatible and preferably will be located on the same filesystem as the path.
* **REST URL**: URL to the "REST" webapp. E.g. ``http://localhost:8080/rest/``;
  for production systems, this address will be slightly different, such as:
  ``https://demo.dspace.org/rest/``.
* **User**: a username for the DSpace instance with sufficient permissions to
  permit authentication.
* **Password**: the password for the username above.
* **Default DSpace DIP collection id**: the UUID of the collection into which
  the DIP will be deposited barring it being designated in a transfer metadata
  file.
* **Default DSpace AIP collection id**: the UUID of the collection into which
  the AIP will be deposited barring it being designated in a transfer metadata
  file.

.. note::

   The following seven fields are optional and can safely be ignored should you
   only require a connection to DSpace.

   Unlike the DSpace via SWORD2 space, which uses the :ref:`Dashboard
   administration tab <archivematica:dashboard-admin>` to configure a single
   ArchivesSpace, the DSpace via REST space gives you the option to configure
   different ArchiveSpaces instances per space.

* **ArchivesSpace URL**: URL to the ArchiveSpace server.
  E.g.: ``http://sandbox.archivesspace.org/``.
* **ArchivesSpace user**: ArchivesSpace username to authenticate as
* **ArchivesSpace password**: ArchivesSpace password to authenticate with
* **Default ArchivesSpace repository**: Identifier of the default ArchivesSpace
  repository
* **Default ArchivesSpace archival object**: Identifier of the default
  ArchivesSpace archival object barring it being designated in a transfer
  metadata file
* **Send AIP to Tivoli Storage Manager**: this is a feature specific to the
  requirements of Edinburgh University which sponsored the development of this
  space. Essentially it executes a bash command using a binary called ``dsmc``.
* **Verify SSL certificates**: Requests verifies SSL certificates for HTTPS
  requests, just like a web browser. By default, SSL verification is enabled,
  and Requests will throw a SSLError if it’s unable to verify the certificate:

.. _dspace:

DSpace via SWORD2 API
+++++++++++++++++++++

DSpace via SWORD2 locations are currently supported only for AIP Storage locations.
Because DSpace is typically used as a public-facing system, the behaviour is different
than when using other access protocols for AIP Storage: upon deposit in DSpace,
the AIP will be split into two parts:

* a compressed objects file (bitstream), which contains all original and
  normalized objects
* as well as a metadata file (bitstream), which contains all of the bag
  artifacts, metadata and logs

The metadata bitstream can optionally be restricted;
:ref:`see below <restrictedmetadata>`. Presently, the Storage Service and Dashboard
are not capable of downloading/reconstituting the AIP - this must be done manually
from the DSpace interface.

If using DSpace as the AIP location in conjunction with the ArchivesSpace
workflow in the :ref:`Appraisal tab <archivematica:appraisal>`, a post Store AIP
hook will send the DSpace handle to the ArchivesSpace digital object record upon
AIP storage. The ArchivesSpace configuration is set up in the
:ref:`Dashboard administration tab <archivematica:dashboard-admin>`.

.. note::

   Note that the DSpace via SWORD2 API space makes use of the DSpace REST API to
   change the permissions of the metadata file. This means that you need to make
   sure that the REST API is configured, see the `DSpace 5 REST API
   documentation`_.

   The DSpace via SWORD2 API space functionality only supports DSpace 5 and not
   other versions. See changes in authentication in the DSpace REST API between
   versions 5 and 6 in the `DSpace 6 REST API documentation`_.

Fields:

.. _restrictedmetadata:

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
* **REST URL**: URL to the "REST" webapp. E.g. ``http://localhost:8080/rest/``;
  for production systems, this address will be slightly different, such as:
  ``https://demo.dspace.org/rest/``.
* **Password**: the password for the username above.
* **Restricted metadata policy**: Use to restrict access to the metadata
  bitstream. Must be specified as a list of objects in JSON, e.g.
  ``[{"action":"READ","groupId":"5","rpType":"TYPE_CUSTOM"}]``. This will
  override existing policies.

.. _fedora:

Fedora via SWORD2
+++++++++++++++++

Fedora via SWORD2 is currently supported in the Storage Service as an Access
Protocol to facilitate use of the
`Archidora plugin <https://wiki.duraspace.org/display/ISLANDORA/Archidora>`_,
which allows ingest of material from Islandora to Archivematica. This workflow
is in beta testing as of Storage Service 0.9/Archivematica 1.5/Islandora
7.x-1.6.

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
     stored by Archivematica. See the :ref:`Administration <administration>`
     section.

.. _gpg:

GPG encryption on local file system
+++++++++++++++++++++++++++++++++++

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
++++++++++++++++

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
++++++

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
+++

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
+++++++++++++++++++++++++

Pipeline Local Filesystems refer to the storage that is local to the
Archivematica pipeline, but remote to the Storage Service. For this Space to
work properly, passwordless SSH must be set up between the Storage Service
host and the Archivematica host.

For example, the Storage Service is hosted on storage_service_host and
Archivematica is running on archivematica1. The transfer sources for
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
+++++

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
+++++++++++

Amazon S3 is available as an access protocol as of Storage Service 0.12.
Locations within S3 can be used as AIP Storage, Replicator and Transfer Source.

Fields:

* **Size**: the maximum size allowed for this space. Set to 0 or leave blank
  for unlimited. This field is optional.
* **Path**: *Not required: see below*.
* **Staging path**: the absolute path to a staging area on the same server as
  the storage service.
* **S3 Bucket**: the name of the designated S3 Bucket. This field is optional.
* **S3 Endpoint URL**: the URL of the S3 endpoint, e.g.
  ``https://s3.amazonaws.com``.
* **Access Key ID to authenticate**: the public key generated by S3. *Not
  required: see below*
* **Secret Access Key to authenticate with**: the secret key generated by S3.
  *Not required: see below*
* **Region**: the region that the S3 instance uses, e.g. ``us-east-2``.

.. note::

   Not all fields are required when configuring S3 storage. They can however
   still be used.

   When a Path is configured for S3 as well as a Relative Path
   for the Location (see `locations`_) the Storage Service will attempt to
   create an S3 bucket to appear as follows to the user (each newline indicates
   a nested directory):

   In this example:

   * ``d6d618dd-2b7f-4177-8b59-10e242066cb7`` is the UUID of the S3 storage
     space that has been configured.
   * ``cd20f886-0c40-4202-af80-399b6ca9f1f1`` is the UUID of the AIP that we
     want to store.
   * ``storage-space-path`` is configured for the Storage Space Path *(no
     leading slash)*.
   * ``storage-location-relative-path`` is configured as Location
     (Relative-path) *(no leading-slash)*.

   The values for the storage space ``Path``. And Location ``Relative Path``
   will appear as configured::

     s3
     └── d6d618dd-2b7f-4177-8b59-10e242066cb7
         └── storage-space-path
             └── storage-location-relative-path
                 └── cd20
                     └── f886
                         │
                         └── ...additional pair-tree folders...
                             │
                             └── f1f1
                                 └── cd20f886-0c40-4202-af80-399b6ca9f1f1.7z


   When Access Key ID and Secret Access Key are configured, the Storage Service
   will attempt to authenticate with those details.

   When these values are not configured, the Storage Service will attempt to use
   authentication details provided by the ``AWS_ACCESS_KEY_ID`` and
   ``AWS_SECRET_ACCESS_KEY`` environment variables.

   When the bucket name is configured, that bucket will be used. Otherwise,
   the ID of the space will be used instead. In either case the bucket
   will be automatically created if necessary, and if the AWS user has
   permissions to do so.

.. warning::

   For the `Path` configured in the Storage Space and `Relative Path`
   configured in the Location, they are best configured without a leading-slash
   (`/`) as this may impact the ability to delete stored packages on the S3
   service. This is an impact of the third-party library used to make S3 API
   calls.

   If users have stored packages on paths with leading slashes and packages are
   not being removed from S3, consider modifying the configuration to remove
   the slash. Do so with caution and be prepared to put the leading-slash
   back if there is any impact on storing AIPs as a result.

.. _locations:

Locations
---------

Locations are contained within spaces and have a defined purpose in the
Archivematica system. Each location is associated with at least one pipeline. A
pipeline can have multiple instances of any location, and a location can be
associated with any number of pipelines, with the exception of Backlog and
Currently Processing locations, for which there must be exactly one per
pipeline.

.. image:: images/Locations.*
   :align: center
   :width: 60%
   :alt: Storage Service locations screen.

A location can have one of nine purposes: AIP Recovery, AIP
Storage, Currently Processing, DIP Storage, FEDORA Deposit, Storage Service
Internal Processing, Transfer Backlog, Transfer Source, or Replicator.

Fields:

* **Purpose**: the function that this location will fulfill, e.g.
  ``AIP storage``. See :ref:`location purposes <location-purposes>` for more
  information.
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

.. note::

   When setting up a :ref:`DSpace via SWORD2 location <dspace>` the relative
   path needs to be the URL of the destination collection for the transfers.
   E.g.: ``https://demo.dspace.org/10673/60/``.

.. _location-purposes:

Location purposes
^^^^^^^^^^^^^^^^^

**AIP Recovery**

AIP Recovery locations are where the :ref:`AIP recovery <recovery>` feature
looks for an AIP to recover. No more than one AIP recovery location should be
associated with a given pipeline. The default value is
``/var/archivematica/storage_service/recover`` in a Local Filesystem. This is
only required if AIP recovery is used.

**AIP Storage**

AIP Storage locations are where the completed AIPs are placed for long-term
storage. For pipelines using the default locations, the default path is
```/var/archivematica/sharedDirectory/www/AIPsStore`` in a Local Filesystem. An
AIP storage location is required to store and retrieve AIPs.

**Currently Processing**

Archivematica uses the Currently Processing location associated with that
pipeline to store materials during active processing. Exactly one currently
processing location should be associated with a given pipeline. For pipelines
using the default locations, the default path is
``/var/archivematica/sharedDirectory`` in a Local Filesystem. A currently
processing location is required for Archivematica to run.

**DIP Storage**

The DIP Storage location is used for storing DIPs until such a time that they
can be uploaded to an access system. For pipelines using the default locations,
the default path is ``/var/archivematica/sharedDirectory/www/DIPsStore`` in a
Local Filesystem. A DIP storage location is required to store and retrieve DIPs,
but it is not required to upload DIPs to access systems.

**FEDORA Deposit**

A FEDORA Deposit location is used with the Archidora plugin to ingest material
from Islandora. This is only available to the FEDORA Space, and is only required
for that space.

**Storage Service Internal Processing**

There should only be one Storage Service Internal Processing
location for a Storage Service installation. For pipelines using the default
locations, the default path is ``/var/archivematica/storage_service`` in a
Local Filesystem. This is required for the Storage Service to run, and must be
locally available to the Storage Service. It should not be associated with any
pipelines.

**Transfer Backlog**

Transfer Backlog locations store transfers until the user continues processing
them. For pipelines using the default locations, the default path is
``/var/archivematica/sharedDirectory/www/AIPsStore/transferBacklog`` in a Local
Filesystem. This is required to store and retrieve transfers in backlog.

**Transfer Source**

A list of Transfer source locations are displayed in the transfer source
dropdown on the Archivematica pipeline's :ref:`Transfer tab
<archivematica:transfer>`. Any folder in a transfer source can be selected to
become a transfer. The default value is ``/home`` in a local filesystem.

**Replicator**

Replicator locations can be configured to replicate the AIPs in one or more AIP
storage locations. If you wish for the replicated AIPs to be encrypted, create
the location in an :ref:`encrypted space <gpg>`.

.. note::

   If you want the same space to have multiple purposes, multiple locations with
   different purposes can be created.

How to configure a location
^^^^^^^^^^^^^^^^^^^^^^^^^^^

To create and configure a new Location:

#. In the Storage Service, navigate to the Spaces tab.
#. Under the space that you want to add the location to, click on **Create
   Location here**.
#. Choose a purpose (e.g. AIP Storage) and pipeline, and enter a "Relative Path"
   (e.g. ``var/mylocation``) and human-readable description. The Relative Path
   is relative to the Path defined in the Space you are adding the Location to.
   For example, for the default Space, the Path is ``/`` so your Location path
   would be relative to that (in the example here, the complete path would end
   up being ``/var/mylocation``).

   .. note::

      If the path you are defining in your Location doesn't exist, you must
      create it manually and make sure it is writable by the Archivematica
      user.

#. For an AIP storage location, choose the replicator location(s) if desired.
#. Save the Location settings.
#. The new Location will now be available as an option under the appropriate
   options in the Dashboard, for example as a Transfer location (which must be
   enabled under the Dashboard "Administration" tab) or as a destination for AIP
   storage.

.. _packages-tab:

Packages
--------

.. image:: images/Packages.*
   :align: center
   :width: 80%
   :alt: Storage Service packages screen.

A package is a file that Archivematica has stored in the Storage Service,
commonly an Archival Information Package (AIP). Dissemination Information
Packages (DIPs) which have been stored and transfers which have been sent to
backlog will also be listed in the Packages tab.

AIPs cannot be created or deleted through the Storage Service interface, though
a deletion request can be submitted through Archivematica that must be approved
or rejected by the Storage Service administrator. To learn more about deleting
an AIP, see :ref:`Deleting an AIP <archivematica:delete-aip>`.

Stored DIPs can be deleted through the Storage Service interface by choosing the "Delete" option in the "Actions" column.

Deletion requests for transfers are automatically generated when all
of the objects from the transfer have successfully been stored in AIPs.

For more information about Fixity Status, see :ref:`Fixity <fixity-docs>`.

.. _administration:

Administration
--------------

The Administration section manages the users and settings for the Storage
Service.

.. image:: images/StorageserviceAdmin.*
   :align: center
   :width: 80%
   :alt: Storage Service Administration screen.

Configuration
^^^^^^^^^^^^^

The configuration page allows you to control the behaviour of the Storage
Service.

**Pipelines are disabled upon creation?** allows you to decide whether a newly
created Archivematica :ref:`pipeline <pipeline>` can access the :ref:`Locations
<locations>` that are assigned to it. By disabling newly created pipelines, you
can provide some security against unwanted perusal of the files in assigned
locations, or use by unauthorized Archivematica instances. This can also be
configured individually when creating a pipeline manually through the Storage
Service website.

**Object counting in spaces is disabled?** allows you to disable automatic
object counting for spaces that display count information to users, like the
space containing the transfer source location. Having object counting enabled
can cause delays and timeouts in the dashboard.

.. figure:: images/object-count-enabled.*
   :align: center
   :width: 80%
   :alt: A list of directory names with object counts beside them - for example, ``archivematica-sampledata (3810 objects)``.

   The transfer browser with object counting enabled.

.. figure:: images/object-count-disabled.*
   :align: center
   :width: 80%
   :alt: A list of directory names with no object counts beside them - for example, ``archivematica-sampledata``.

   The transfer browser with object counting disabled.

Recovery request
++++++++++++++++

These fields allow you to set up notifications for events related to the AIP
deletion workflow - for example, deletion approvals or rejections.

Fields:

* **Recovery request: URL to notify:** the server that should receive the
  JSON-encoded event message from the Storage Service.
* **Recovery request notification: Username (optional):** A username for basic
  access authentication.
* **Recovery request notification: Password (optional)** A password for basic
  access authentication.

Default locations
+++++++++++++++++

The default location settings allow you to define default locations for any new
pipeline that is registered with the Storage Service. You can define default
locations for the following:

* Transfer source
* AIP storage
* DIP storage
* Transfer backlog
* AIP recovery

Multiple transfer source or AIP storage locations can be configured by holding
down ``Ctrl`` when selecting them.

A Currently Processing location is also created for every new pipeline, since
one is required.

Users
^^^^^

The Storage Service has two types of users: administrative users, and regular
users. The only distinction between the two types is for email notifications;
administrators will be notified by email when special events occur, while
regular users will not. Only registered users can long into the Storage Service.

Version
^^^^^^^

The version page will display the current version and specific git commit of
your installation of the Storage Service.

Service callbacks
^^^^^^^^^^^^^^^^^

Callbacks allow Archivematica Storage Service to make REST calls after
performing certain types of actions, so that external services are notified when
internal actions have taken place.  You can created callbacks to alert external
services when an AIP, DIP, or AIC has been stored.

To create a new callback, click on **Create new callback**. This will bring you
to a form where you can enter the callback information.

Fields:

* **Event**: Type of event when this callback should be executed (i.e.
  post-store AIP, post-store DIP)
* **URI**: URL to contact upon callback execution.
* **Method**: HTTP request method to use in connecting to the URL (i.e. GET,
  POST)
* **Headers (key/value)**: the header(s) for the request.
* **Body**: Body content for each request. Set the 'Content-type' header
  accordingly.
* **Expected status**: Expected HTTP response from the server, used to validate
  the callback response.
* **Enabled**: check the box to enable the callback.

Event types available include:

* **Post-store AIP (source files)**: Occurs after an AIP has been stored and
  causes the execution of a request for each source file of the AIP.
* **Post-store AIP, Post-store AIC and Post-store DIP**:  Occurs after an AIP,
  AIC or DIP has been stored and causes the execution of a single request for
  the package.

You can use the following placeholders in the URI and Body fields:

* **Post-store AIP (source files)**: ``<source_id>`` will be replaced by the
  source file UUID.
* **Post-store AIP, Post-store AIC and Post-store DIP**: ``<package_uuid>``
  will be replaced by the AIP, AIC or DIP UUID. ``<package_name>`` will be
  replaced by the AIP, AIC or DIP name, with the trailing UUID removed.

.. note::

   For AIPs created directly from a transfer, the value that replaces
   ``<package_name>`` will be equal to the name of the transfer after
   successful completion of the "Change transfer filenames" Microservice.

External applications can integrate with Archivematica via the `post_store`
API endpoint or `stored` endpoints. See the `API documentation`_  for more
information on using these endpoints.

A callback can be configured for the SCOPE integration. See the
`SCOPE documentation`_ for how to set up this callback.

Encryption keys
^^^^^^^^^^^^^^^

GPG encryption keys can be created or imported to be used in spaces to store
encrypted AIPs, transfers or replicated AIPs/transfers. Keys can either be
created by the Storage Service or imported.

To create a new key:

1. Click on **Create New Key**

2. Enter the name and email address you want associated with the key.

To import a key:

1. Click on **Import Existing Key**

2. Paste in your key in ASCII-armored format.

Language
^^^^^^^^

Configure language settings for the Storage Service in this area of the
Administration tab. Strings are available for translation on the localization
platform (Transifex).

:ref:`Back to the top <administrators>`

.. _`API documentation`: https://wiki.archivematica.org/Storage_Service_API#AIP_storage_callback_request
.. _`LOCKSS`: http://www.lockss.org/
.. _`Dataverse api guide`: http://guides.dataverse.org/en/latest/api/search.html
.. _`DSpace 5 REST API documentation`: https://wiki.duraspace.org/display/DSDOC5x/REST+API
.. _`DSpace 6 REST API documentation`: https://wiki.duraspace.org/display/DSDOC6x/REST+API#RESTAPI-Index/Authentication
.. _`SCOPE documentation`: https://github.com/CCA-Public/scope
