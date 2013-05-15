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

Ingester API (Application Programming Interface)
------------------------------------------------

The project has been developed with the Ingester API to increase its flexibility, the Ingester API integrates the provisioning interface and ingester platform or in other words it separates the user interface and project functionality from the data and data ingestion/streaming.

The ingester API is object oriented where most functionality is implemented through generic model methods (eg. insert, update post, ...) and there are a handful of methods for specific functionality.  

Most of the API functions can be used within a unit of work allowing for many calls to be used atomically (eg. all work or all fail rather than failing half way through and leaving the system in an unknown state).

Project Structure
+++++++++++++++++

The ingester API source files are structured with general API code in the base folder, all models in the models folder and schema definitions within the schemas folder.

The main API file that contains all methods is ingester_platform_api.py.

API Methods
+++++++++++

API Methods are all implemented in the ingester_platform_api.py, this file also contains other useful classes such as Marshaller which converts ingester API models to and from dicts.

IngesterPlatformAPI methods:

+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| __init__           | Requires the data layer’s address as well as an authentication object.                                                                                |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| ping               | A simple diagnotic method which should return "PONG"                                                                                                  |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **post**           | Create a new entry or update an existing entry using the passed in object, the entry type will be based on the objects type (Included in UnitOfWork). |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **insert****       | Create a new entry using the passed in object, the entry type will be based on the objects type (Included in UnitOfWork).                             |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **update**         | Update an entry using the passed in object, the entry type will be based on the objects type (Included in UnitOfWork).                                |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **delete**         | Delete an entry using the passed in object, the entry type will be based on the objects type (Included in UnitOfWork).                                |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| search             | Not implemented yet.                                                                                                                                  |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **commit**         | Commit a unit of work (Included in UnitOfWork).                                                                                                       |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **enableDataset**  | Enable data ingestion for this dataset (Included in UnitOfWork) .                                                                                     |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| **disableDataset** | Disable data ingestion for this dataset (Included in UnitOfWork).                                                                                     |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| getIngesterLogs    | Get all ingester logs for a single dataset.                                                                                                           |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| getRegion          | Get the region object specified by the given id.                                                                                                      |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| getLocation        | Get the location object specified by the given id.                                                                                                    |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| getSchema          | Get the schema object specified by the given id.                                                                                                      |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| getDataset         | Get the dataset object specified by the given id.                                                                                                     |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| getDataEntry       | A data entry is uniquely identified by dataset id + data entry id.                                                                                    |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| reset              | Resets the service                                                                                                                                    |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| findDatasets       | Search for datasets                                                                                                                                   |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| createUnitOfWork   | Creates a unit of work object that can be used to create transactional consistent set of operations.                                                  |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| close              | Close should always be called when finished with the IngesterPlatformAPI to allow it to clean up any related data.                                    |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+

Models
++++++

The ingester API contains a number of models for representing the different types of data and configurations required for ingestion and storage.

+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Data Entry      | Data Entries model the actual data that is ingested, they require a valid timestamp, location and dataset and their data attribute will contain the ingested data with indexes as configured by the datasets’ DataEntrySchema.                                                                                  |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Data Sources    | Data sources configure how data can be ingested.  Implemented data sources include DatasetDataSource, PullDataSource, PushDataSource, SOSScraperDataSource and FormDataSource.  Each of the data sources mentioned require specific configurations.                                                             |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Dataset         | A Dataset represents a collection of data associated with a sensor or data method and hold the full configuration for an ingester.  All DataEntry’s are associated with a Dataset.                                                                                                                              |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Locations       | There are currently two types of location which are Location and OffsetLocation.  Locations represent a point on earth and there must be a Location object associated with each Dataset and there may be an OffsetLocation associated with a Dataset.  OffsetLocations provide an offset from the set Location. | 
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Region          | Represents a 2D area on earth (eg. Queensland)                                                                                                                                                                                                                                                                  |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Metadata Entry  | Metadata is extra information (eg. QA information) that can be attached to other models, this is useful as a flexible way of adding real world information when the requirements are unknown at the time of developing.                                                                                         |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Sampling        | Some data sources poll external systems and _Sampling objects indicate when this should occur.  There is currently only one implementation (PeriodicSampling) that samples at a set interval.                                                                                                                   |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Search Criteria | Search criteria is intended to provide advanced search features but hasn’t been fully implemented at the time of writing.                                                                                                                                                                                       |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Ingester Log    | Represents a single dataset and contains the information required to ingest the data as well as location metadata.                                                                                                                                                                                              |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Schemas
+++++++

Schemas allow flexible data storage configuration and are all based from the Schema class in schemas/__init__.py.

Schemas are minimal objects that can be extended with new (typed) attributes as well as inheriting attributes from parent schemas.  

Schemas are generated from the data configurations section on the methods page of EnMaSSe.

There are three subclasses of Schema:

#. **DataEntrySchema** is the primary schema that is used for all data configurations.
#. **DataEntryMetadataSchema** can be used to attach metadata (eg. QA information) to data.
#. **DatasetMetadataSchema** can be used to attach metadata to datasets.


All schemas begin with 3 attributes:

- name
- id
- version


Additional attributes can be added using the addAttr() method, new attributes must be instances of DataType and will typically include:

- description
- name
- units

Parent schemas can be added using the extends() setter, the provided values must be a list of valid schema id’s.

XML-RPC Data Layer API
----------------------

The ingester platform data layer is provided over an XML-RPC API which is implemented using the ingesterapi->ingester_platform_api->Marshaller() and LXML libraries to convert the Ingester Platform domain objects to and from XML.

Refer to the Ingester Platform developer documentation for more details on the domain objects used.


.. toctree::
   :maxdepth: 2

   provisioning-developer   
   ingester-developer
   reusability
