.. _recovery:

============
AIP recovery
============

AIP recovery allows a Storage Service administrator to replace a corrupt version
of a stored AIP with the correct version. This process requires that you have a
non-corrupted backup copy of the AIP. By replacing the AIP using the recovery
process, the recovered version of the AIP will be available to users through the
Archivematica dashboard.

*On this page*:

* :ref:`Performing an AIP recovery <how-recovery-works>`
* :ref:`Example recovery request <example-recovery-request>`
* :ref:`Reporting recovery progress to external systems <reporting-recovery-progress>`

.. _how-recovery-works:

Performing an AIP recovery
--------------------------

#. Determine the path of the *AIP recovery* directory. You can find the path of
   this directory by clicking on the **Locations** tab in the Storage Service
   user interface, then finding the path that has the purpose "AIP Recovery".
   This is the AIP recovery location.

   .. image:: images/aip-recovery-location.*
      :align: center
      :width: 80%
      :alt: The Locations tab has a table with all Storage Service locations listed. The AIP recovery location has the purpose "AIP Recovery" in the first column.

#. Copy the recovered version of the AIP to the AIP recovery location.

#. Using the Storage Service REST API, make a
   :ref:`recovery request <example-recovery-request>`.

#. Approve the AIP recovery request by going to the **Packages** tab in the
   Storage Service user interface, then clicking on **View recovery requests**.
   Provide a reason for the recovery and click **Approve**.

#. Once the recovery has been approved, the Storage Service will do the
   following:

   * Check the fixity data of the recovered AIP. Recovery will fail if this
     initial fixity check fails.
   * Copy the corrupt AIP to a backup directory within the recovery location.
   * Copy the recovered AIP to AIP storage.
   * Check the fixity data of the stored AIP. Recovery will fail if this second
     fixity check fails.

The AIP will now be available for download in the Packages tab of the Storage
Service user interface, as well as through the Archival Storage tab of the
Archivematica dashboard.

.. _example-recovery-request:

Example recovery request
------------------------

Once the recovered AIP is in place, you must make an API request using the
Storage Service REST API. Here is an example:

.. code:: bash

   curl --data="event_reason=<description>&pipeline=<pipeline UUID>&user_id=<int>&user_email=<email>" http://127.0.0.1:8000/api/v2/file/<package UUID>/recover_aip/?format=json

Replace the placeholders with the following information:

* ``<description>``: a description of why the recovery request is being made.
* ``<int>``: the numerical user ID. To find the user ID, go to the
  **Administration** tab and click on **Users** in the lefthand sidebar. Click
  on **Edit** for the user who is making the request. The user ID is shown in
  the URL of the user edit page - for example, the user ID for
  ``http://my-site:8000/administration/users/1/edit/`` is **1**.
* ``<email>``: the email address of the user making the request.

.. _reporting-recovery-progress:

Reporting recovery progress to external systems
-----------------------------------------------

AIP recovery can be optionally configured to report progress to another system
via a REST POST call. The call will report whether the AIP recovery task has
been approved, rejected, or has failed. The information sent includes the ID of
the recovery request, whether the recovery request was processed successfully,
and a text message including the recover request status ("APPROVE" or "REJECT")
and the administrator's reasoning for the decision.

The following is an example of the JSON sent by this POST call:

.. code:: bash

   {
     "event_id": 127,
     "message": "APPROVE: Request approved, files look good.",
     "success": true
   }

To configure the Storage Service to report AIP restore progress to another
system, click **Administration** in the Storage Service user
interface and enter a REST endpoint URL into the **Recovery request: URL to
notify** field.

If the endpoint requires authentication, you'll also need to enter a username
and password into the two fields below it. Click **Save** when you are done.

:ref:`Back to the top <recovery>`
