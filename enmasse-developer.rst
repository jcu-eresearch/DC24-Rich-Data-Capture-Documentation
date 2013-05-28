=================
EnMaSSe Developer Guide
=================

*This guide explains the project structure and implementation details of the Environmental Monitoring and sensor storage (EnMaSSe) application.*

*This guide doesn’t cover how to use the system or how to do general administration and it is recommended that administrators first read the user and administrator guides.*

The EnMaSSe application is designed to provide a user friendly interface for:

- Flexible and scalable research data ingestion (both streamed or manually input).
- High quality, fine grained, project based, metadata creation and export (eg. Enter 1 record, export many).
- Administerable and maintainable project lifecycle and workflows.


The project has been developed in three components:

- **Provisioning interface** provides a user friendly experience for creating metadata records, getting data type and ingestion configurations and managing the project.
- **Ingester platform** handles data ingestion, indexing and storage.
- **Ingester API** provides separation of the ingester platform from the provisioning interface.

EnMaSSe integrates with the following 3rd party components:

- **CoastalCOMS Digital Asset Manager (CC-DAM)** as scalable, persistent data storage (used by the ingester platform).
- **ReDBox** is an open source metadata repository that among other things, publishes metadata records nationally to Australian National Data Services (used by the provisioning interface).

Other core frameworks used by EnMaSSe include:

- **Pyramid** is the web framework used by the provisioning interface.
- **Deform** provides python object based form creation.
- **SQLAlchemy** is used for object oriented database access.
- **ColanderAlchemy** wraps both Deform and SQLAlchemy models together so the same models are used for forms and database access.
- **MySQL** is the database used in both the provisioning interface and ingester platform (not the ingester platform only uses it for local configurations not actual data storage).
- **Shibboleth** is a federated single sign-on framework that provides secure and controlled authentication, and release of user attributes.
- **UWSGI** is a more efficient web container for Pyramid than alternative HTTP containers.
- **XMLRPC** is used by the ingesterapi and ingester platform to provide platform independent programming interfaces.

*It is recommended that you read the Ingester API first.*

.. toctree::
   :maxdepth: 2
   
   ingesterapi-developer
   provisioning-developer   
   ingester-developer
   reusability

Information on Implementing Specific Functionality
--------------------------------------------------

Adding Data Source’s
++++++++++++++++++++

The most useful and generic data source currently implemented is the PullDataSource, so throughout this explanation we will use it as the example.

Provisioning Interface
======================

#. Add a new entry to models->project.py->Method->data_sources.
#. Update the description of models->project.py->Method->data_source.
#. Copy/Paste models->project.py->PullDataSource and update as needed, this provides the data source configuration options on the datasets page.
#. Copy/Paste models->project.py->Dataset->pull_data_source and update for your newly created data source type.
#. Update controllers->ingesterapi_wrapper.py->_create_data_source to convert your new data source configuration to the corresponding `Ingester API`_ data source model you have/will create.

Ingester API
============

#. Copy/Paste models->data_sources.py->PullDataSource and update with the fields and initialisers that your new data source requires.

Ingester Platform
=================
**TODO: This section needs updating.**

Custom Import Scripts
+++++++++++++++++++++


.. _`ingester-post-processing-scripts`: https://tdh-rich-data-capture-documentation.readthedocs.org/en/latest/ingester-developer.html#ingester-post-processing-scripts
See `Ingester Post Processing Scripts`_ in the Ingester Platform developers guide.

Limitations & Future Work
-------------------------

**TODO: This section needs updating.**
