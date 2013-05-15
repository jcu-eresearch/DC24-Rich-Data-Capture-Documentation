=================
Project Reusability
=================

Full user, administrator and developer documentation is provided allowing full reuse of the developed project.  

EnMaSSe has been designed to be as reusable as possible but it is a specialised tool with a small group of potential users:

- The full EnMaSSe system can be reused for other universities or research organisations.
- The ingester platform and provisioning interface are integrated through the ingester API, so either system could be reused separately.
- Extended form functionality could be pushed to the Deform and ColanderAlchemy projects.

EnMaSSe System
--------------

The project has been developed with a number API’s to increase the flexibility of reuse:

- **ingesterapi** integrates the provisioning interface and ingester platform or in other words it separates the user interface and project functionality from the data and data ingestion/streaming.
- The **data layer JSON-RPC API** separates the data storage from the data ingestion/streaming functionality.
- ReDBox is integrated with two scripts that exports rows from the provisioning interface metadata table data as XML records and generate ReDBox configurations (There are also ReDBox specific files external to the EnMaSSe system).


The only component that isn’t open source is the CoastalCOMS Digital Asset Manager (CC-DAM) which is proprietary 3rd party software.  CoastalCOMS is open to working with organisations to use their CC-DAM but the ingester platform is separated from the data layer through a JSON-RPC API and a custom data layer could be implemented relatively easily.

ReDBox is an open source project under active development for research organisations and it is likely that potential users will already have a ReDBox instance.  Integration with ReDBox is implemented in 2 files:

- src/jcu.dc24.provisioning/jcudc24provisioning/controllers/redbox_mint.py which prepares the metadata records, writes them as XML files, uploads them to the ReDBox server and alerts ReDBox that there is new data available.
- src/jcu.dc24.provisioning/jcudc24provisioning/scripts/create_redbox_config.py generates XPATH mappings from the exported EnMaSSe XML files to ReDBox fields.


This means that not only is the ReDBox implementation separated from the provisioning interface but also that other metadata systems could be integrated with ease.

The real power and reusability of the EnMaSSe is directly streaming sensor data into a scalable data storage system in a highly flexible way:

- Reusable and extendable for almost any type of streaming sensor.
- Highly customisable data configurations and custom processing scripts mean the data storage can index the ingested data any way the user requires while the bulk of data is stored as flat files (more scalable).
- Custom processing scripts can be chained together allowing the data to be split and processed in many ways.

Ingester Platform
-----------------

The ingester platform is designed to facilitate the retrieval, aggregation, processing, and ingestion of data from disparate sources into a central repository. The platform supports a number of types of data source, providing a number of implementations for retrieving data from remote websites or webservices. As well, the data processing step is completely pluggable. Finally, repository access is abstracted so that new repositories can be easily supported. The implementations provided includes a reference implementation using an SQL database, and the CC-DAM repository.

Provisioning Interface

The problem with reuse of the EnMaSSe provisioning interface is that it is a wrapper of other components to provide a single, user friendly interface rather than a tool on its own.

To break reuse possibilities up the core things the provisioning interface does are:

- Creates project based metadata records (eg. enter one generate many).
- Provides an interface for generating data configurations (ie. database tables).
- Exports metadata to ReDBox.
- Exports ingestion configurations to the ingester platform.

Project Based Metadata
++++++++++++++++++++++

So if someone was looking to create a project based metadata tool with a different focus than ingesting streamed data they could reuse a large portion of the EnMaSSe project by:

- Editing the methods and datasets pages to implement their required functionality.
- Alter the submit page functionality to fit their required workflow.
- Alter the workflows.py->generate_dataset_record to generate child records as required,
- No alterations or fixes would be required to keep integration with ReDBox.
- Update the general website pages.

Database Schema/Form Creation Interface
+++++++++++++++++++++++++++++++++++++++

The data configurations is a component that could be reused separately either to create database schemas, forms or both.

With a bit of interface work this could be developed into a handy web development tool.

ReDBox Integration
++++++++++++++++++

The ReDBox integration is a smaller, application specific component however it does provide a good example of integrating with the new ReDBox alerts harvester.

Deform Templates & Widgets
--------------------------

To get the required functionality a decent amount of additional functionality has been implement as Deform widgets and templates.

The most notable improvements are the addition of help, description and placeholder to all elements where appropriate.

ColanderAlchemy Functionality
-----------------------------

ColanderAlchemy is a relatively young framework that joins SQLAlchemy with Colander (Deform uses Colander for it’s models) which means that ColanderAlchemy models generate both user interface forms and database tables.

As part of the core functionality of the EnMaSSe provisioning interface, models and scripts were written for:

- Converting deform appstructs into ColanderAlchemy models.
- Converting ColanderAlchemy models into deform appstructs.
- Implements additional display options such as displaying specified fields as grouped.
- Converting ColanderAlchemy models to XML.




