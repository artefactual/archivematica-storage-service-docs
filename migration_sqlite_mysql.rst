.. _migration-sqlite-mysql:

===================================
Migrating data from SQLite to MySQL
===================================

Storage Service supports both SQLite and MySQL databases. In most cases, it is
preferably to use the latter since SQLite does not support concurrent writes.
This is exarcerbated by the fact that Storage Service performs long write
transactions.

.. note:

   Check out `issue #952`_ for more details.

Please find below an overview of the steps needed to move your data to MySQL.
Make sure that you take a back up of your SQLite database before you proceed.

1. Stop Storage Service::

    systemctl stop archivematica-storage-service

2. Export your data to JSON::

    su - archivematica -s /bin/bash -c "set -a -e -x; source /etc/default/archivematica-storage-service; cd /usr/lib/archivematica/storage-service/; /usr/share/archivematica/virtualenvs/archivematica-storage-service/bin/python manage.py --exclude auth.permission --exclude contenttypes --exclude=sessions.session --natural-foreign --natural-primary --indent 4 dumpdata > /tmp/datadump.json"

3. Create the MySQL database::

    CREATE DATABASE storage_service_test CHARACTER SET utf8 COLLATE utf8_general_ci;

4. Update the service configuration, e.g. in
   ``/etc/default/archivematica-storage-service``, remove ``SS_DB_NAME`` and add
   instead the following::

    SS_DB_URL=mysql://username:password@hostname:3306/storage_service_test

5. Create the MySQL tables::

    su - archivematica -s /bin/bash -c "set -a -e -x; source /etc/default/archivematica-storage-service; cd /usr/lib/archivematica/storage-service/; /usr/share/archivematica/virtualenvs/archivematica-storage-service/bin/python manage.py migrate"

6. Load the JSON export::

    su - archivematica -s /bin/bash -c "set -a -e -x; source /etc/default/archivematica-storage-service; cd /usr/lib/archivematica/storage-service/; /usr/share/archivematica/virtualenvs/archivematica-storage-service/bin/python manage.py loaddata /tmp/datadump.json"

.. _issue 952: https://github.com/archivematica/Issues/issues/952
