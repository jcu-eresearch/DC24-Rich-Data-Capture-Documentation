.. Environmental Monitoring and Sensor Storage (EnMaSSe) documentation master file, created by
   sphinx-quickstart on Thu Jan 31 14:01:41 2013.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Environmental Monitoring and Sensor Storage (EnMaSSe) documentation!
===============================================================

The EnMaSSe documentation is broken into three sections:

- User guide explains the application as a whole, application concepts and how to use it.
- Administrator guide focuses on installation, expected changes that administrators will need to do and general administration during general usage.
- Developer guide outlines the frameworks and libraries used, the project structure and the technical details behind how it was developed. 

All three guides focus on different areas and it is recommended that they are read in the order explained above.


Contents:

.. toctree::
   :maxdepth: 2

   enmasse-user
   Using the Data Portal<http://coastalcomms.com.au/portal-user-guide.pdf>
   enmasse-admin
   enmasse-developer  


Nomanclature
============

Within this document the following definitions are assumed.

====================== ==========
Term                   Definition
====================== ==========
user                   An end user, such as a researcher or student.
admin                  A system administrator who will be responsible for installing, configuring, and administering the entire EnMaSSe platform.
developer              Someone with technical skills and understanding who will modify the source code of the EnMaSSe system.
EnMaSSe                The application this documentation is written for (it is a data and metadata collection tool).
Provisioning Interface Internal component of the EnMaSSe application the provides the user interface (website).
Ingester API           Internal component of the EnMaSSe application the provides the Application Programming Interface (API) between the Provisioning Interface and Ingester Platform (how they communicate).
Ingester Platform      Internal component of the EnMaSSe application the provides the data ingestion and storage.
Dataset                A collection of data from a method at a set location.
Method                 A way that data is collected and stored.
====================== ==========


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

