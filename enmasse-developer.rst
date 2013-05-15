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

.. toctree::
   :maxdepth: 2
   
   ingesterapi-developer
   provisioning-developer   
   ingester-developer
   reusability
