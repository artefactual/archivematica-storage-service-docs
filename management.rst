.. _management:

===================
Management commands
===================

Archivematica implements multiple management commands using the command
interface provided by the Django web framework.

A full list of commands can be retrieved as follows:

.. code:: bash

   sudo -u archivematica bash -c " \
	    set -a -e -x
	    source /etc/default/archivematica-storage-service || \
	        source /etc/sysconfig/archivematica-storage-service \
	            || (echo 'Environment file not found'; exit 1)
	    cd /usr/lib/archivematica/storage-service
	    /usr/share/archivematica/virtualenvs/archivematica-storage-service/bin/python manage.py help
  ";

If you want to see the help message of a specific command, try:

.. code:: bash

   sudo -u archivematica bash -c " \
       set -a -e -x
	    source /etc/default/archivematica-storage-service || \
	        source /etc/sysconfig/archivematica-storage-service \
	            || (echo 'Environment file not found'; exit 1)
	    cd /usr/lib/archivematica/storage-service
	    /usr/share/archivematica/virtualenvs/archivematica-storage-service/bin/python 
           manage.py help create_aip_replicas
   ";

We've looked up the command for
:ref:`creating new AIP replicas <creating-aip-replicas>` above.

*On this page:*

* :ref:`Creating new AIP replicas <creating-aip-replicas>`

.. _creating-aip-replicas:

Creating new AIP replicas
-------------------------

The Archivematica Storage Service includes a management command for creating
and re-creating AIP replicas.

By default, the script will create a new AIP replica for every AIP in the
default AIP storage location in each of that AIP storage location's configured
Replicator locations.

.. code:: bash

    sudo -u archivematica bash -c " \
	    set -a -e -x
	    source /etc/default/archivematica-storage-service || \
	        source /etc/sysconfig/archivematica-storage-service \
	            || (echo 'Environment file not found'; exit 1)
	    cd /usr/lib/archivematica/storage-service
	    /usr/share/archivematica/virtualenvs/archivematica-storage-service/bin/python manage.py create_aip_replicas

The command accepts the following parameters:

* ``--aip-uuid`` may be passed optionally to only create replicas of the AIP
  that has the matching UUID.
* ``--delete`` will delete existing replicas in the AIP storage location prior
  to creating new ones.
* ``--aip-store-location`` may be passed optionally to specify an AIP storage
  location other than the default. Replicas will be created of AIPs in the AIP
  storage location that has the matching UUID.
* ``--replicator-location`` may be passed optionally to specify a Replicator
  location in which replicas should be created. Replicas will only be created
  in the Replicator location that has the matching UUID. This may be useful to
  replicate already stored AIPs into a new Replicator location without needing
  to reingest or touch existing replicas in other Replicator locations.

:ref:`Return to the top <management>`
