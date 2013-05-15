=================
Developing the Provisioning Interface
=================
The provisioning interface is based on a number of frameworks and integration with external components, so the first step to development is gaining a high level understanding of:

.. _Pyramid: http://pyramid.com.au
.. _Deform: http://pyramid.com.au
.. _SQLAlchemy: http://pyramid.com.au
.. _ColanderAlchemy: http://pyramid.com.au
.. _MySQL: http://pyramid.com.au
.. _ReDBox: http://pyramid.com.au
.. _`Ingester API`: ingesterapi-developer
.. _`Tal/Metal`: http://pyramid.com.au
.. _jQuery: http://pyramid.com.au
.. _Open Layers: http://pyramid.com.au
.. _Javascript Layers: http://pyramid.com.au
.. _`ZPT template`: 
.. _Fanstatic: 
.. _`Ingester Platform`: 
.. _`Ingester Post Processing Scripts`: ingester-developer

+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Framework        | Why you need to know?                                                               | Description                                                                                                                                                       |
+==================+=====================================================================================+===================================================================================================================================================================+
| Pyramid_         | How the application works as a whole. How views are set-up.                         | Web framework used by the provisioning interface.                                                                                                                 |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Deform_          | How models are turned into forms.  Editing of models.                               | Provides python object based form creation.                                                                                                                       |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| SQLAlchemy_      | How models represent the underlying database.  Editing of models.                   | Used for object oriented database access.                                                                                                                         |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ColanderAlchemy_ | Model attributes and how models relate to Deform as well as SQLAlchemy.             | Wraps both Deform and SQLAlchemy models together so the same models are used for forms and database access.                                                       |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| MySQL_           | In-depth debugging of database issues.                                              | Database used in both the provisioning interface and ingester platform (not the ingester platform only uses it for local configurations not actual data storage). |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ReDBox_          | High level understanding of the metadata records. Debugging metadata record export. | The metadata repository that publishes records to Australian National Data Services (ANDS).                                                                       |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `Ingester API`_  | In-depth understanding of data ingestion and storage. Debugging ingester issues.    | Used to integrate with the ingester platform (ingests and stores data).                                                                                           |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `Tal/Metal`_     | Customisation of template logic and dynamic data.                                   | Allows python data and minor logic to be used in the HTML templates.                                                                                              |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| jQuery_          | Customisation of template scripting.                                                | Javascript library used in most templates.                                                                                                                        |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `Open Layers`_   | Understanding and customising of embedded maps.                                     | Javascript library used to display maps within the information and datasets pages.                                                                                |
+------------------+-------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Framework Modifications
-----------------------

SQLAlchemy + Deform + ColanderAlchemy
+++++++++++++++++++++++++++++++++++++

In short ColanderAlchemy_ allows a single model for both Deform_ and SQLAlchemy_ by creating SQLAlchemy_ models using drop in replacements for Column and relationship with custom deform attributes.  

The Deform schema is generated using SQLAlchemyMapping(<model class>), this schema is then used to display forms and data read from the database could be displayed by calling the dictify() method and passing it to the form.render() method.

ColanderAlchemy Modifications & Usage
+++++++++++++++++++++++++++++++++++++

The EnMaSSe system required a number of extra features that have been implemented in the models/ca_model.py, views/ca_scripts.py and template files:

- CAModel provides additional functionality to ColanderAlchemy_ models when added as a parent (eg. MyModel(CAModel, Base): ...).  Extra functionality includes:
    - dictify() with support for other EnMaSSe specific changes.
    - update()
    - to_xml()
- ca_scripts.py->convert_schema() alters the ColanderAlchemy generated schema to add additional display options including:
    - Removing the top level title (ColanderAlchemy outputs a schema with a name at the top of every form).
    - Removing items not on the specified page (based on attributes on schema nodes).
    - Allowing form elements to be required.
    - Grouping of nodes under a MappingSchema for display purposes.
    - Prevent duplicate field names causing problems by adding parent schema names separated by ‘:’.
    - Removal of advanced/restricted fields based on a passed in parameter (this could be upgraded to integrate with the Pyramid permissions system).
    - There is also a fix_schema_field_name) method that reverts names to their original value.
- Most template files have been modified to support or modify help, description and placeholder text.  Other features have been implemented on a per template basis as well.


Usage of the modified ColanderAlchemy_ models has been made as straightforward as possible:

- Extend all database/form models from both CAModel and SQLAlchemy_ Base (in that order).
- Wrap the ColanderAlchemy_ schema generation with convert_schema giving convert_schema(SQLAlchemyMapping(<model class>), **kw).
- Use the additional custom attributes supported by the modifications.


Custom attributes include:

+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Attribute         | Description                                                                                                                                                                                                                                                                                             |
+===================+=========================================================================================================================================================================================================================================================================================================+
| ca_help | Shows the value as HTML code hidden under a ? symbol next to the elements title.                                                                                                                                                                                                                                  |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_placeholder    | Displays greyed out text that disappears when clicked (only text elements).                                                                                                                                                                                                                             |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_child_         | Uses the following attribute on children rather than the element it is added to (only mapping or sequences), for example child_title would set the title of all child schema items.                                                                                                                     |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_force_required | Set the form element as required.                                                                                                                                                                                                                                                                       |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_restrict_admin | Remove this field if convert_schema() is called with restrict_admin=True.                                                                                                                                                                                                                               |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_page           | Remove this field if convert_schema() is called with a page value that is different.                                                                                                                                                                                                                    |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_group_         | Uses the following attribute on the parent group rather than the element it is added to, for example group_title would set the title of the surrounding mapping (This is mainly used in conjunction with group_start).                                                                                  |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_collapsed      | A +/- symbol is displayed next to the mapping’s title, when this symbol is clicked it shows/hides respectively (only mapping’s).  If the value is True the mapping will be hidden on page load otherwise it will be displayed.                                                                          |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_group_start    | Surround this element and following elements in a mapping (for displaying grouped elements together).  Other group_ attributes would typically be used to improve the mappings display such as group_title, group_help, group_collapsed.  The value identifies which mapping for the closing group_end. |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ca_group_end      | Ends surrounding mappings created with a group_start attribute.  If there is no open group than this attribute will do nothing.  If there is a second group mapping nested within this group it will close both.                                                                                        |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Note:  All attributes on ColanderAlchemy_ models are appended with ca_, if Deform models are used directly the ca_ should be removed from attributes.**

Customised Deform Widgets & Templates
+++++++++++++++++++++++++++++++++++++

There were a limited number of custom Deform_ widgets created which are located in:

- **models->file_upload.py** has minor changes to FileUploadWidget for Provisioning Interface specific storage.
- **views->deform_widgets.py** implements:
    - **ConditionalCheckboxMapping** which hides/shows the following Deform_ element based on if the checkbox is selected.
    - **MethodSchemaWidget** which provides customised functionality for custom fields and standardised fields.


Most of the customisations were implemented in the templates and without going through each of the 50 or so files customised the general pattern followed was:

- If it is functionality specific to the Provisioning Interface the template goes in templates->custom_widgets, otherwise they go in templates->widgets.
- Copy the closest Deform template and modify to add required functionality.
- If the template requires information that isn’t passed in from the widget, directly add it to the schema in the view and access it from the template through field.schema.<name>.
- Javascript_ for widgets that occur many times is implemented as a function in static->scripts->widgets.js, Javascript that can only occur once may stay in the template.

Project Structure
-----------------

The Provisioning Interface structure is organised as:

- **data** - session data and isn’t used by Provisioning Interface otherwise.
- **jcudc24provisioning** - contains all Provisioning Interface source code
    - **Views** - all served websites, these are closer to view-controllers though as they contain much of the applications functional code.
    - **Models** - all database and form models.
    - **Controllers** - additional functional code that doesn’t fit in the views, this is mostly code for integrating with other systems.
    - **Scripts** - initialisation command line scripts that are output to the bin folder when the project is deployed.
    - **Static** - static resources such as images, css and javascript/javascript libraries.
        - **css**
        - **images**
        - **libraries** - Javascript_ libraries
        - **scripts** - custom Javascript_
    - **Templates** - `ZPT template`_ files used by Deform_ widgets and the main website pages.
        - **custom_widgets** - custom, Provisioning Interface specific widget templates
        - **widgets** - customised Deform_ widget templates.
- **project_uploads** - Attachments that are uploaded through the provisioning interface pages.
- **tmp** - temporary files such as for exporting to external systems.

The most relevant files are:

- **__init__.py** initialises the database, configures all views and sets up authentication.  Look at this view to find out what URL’s associated with views and to add new views.
- **resources.py** packages static resource files for Fanstatic_.
- **views->views.py** contains all non-project views.
- **views->workflows.py** contains all project views.
- **scripts->initializedb.py** populates the database the first time the application starts.
- **scripts->create_redbox_config.py** creates the XML mapping file for ReDBox_ integration.
- **models->ca_model.py** wraps CAModel to provide transparent model-dict-xml conversions.
- **models->project.py** contains all project related database and/or form models.
- **models->website.py** contains all non-project database and.or form models.
- **controllers->redbox_mint.py** is the code that handles the metadata exports to ReDBox.
- **controllers->ingesterapi_wrapper.py** wraps the `Ingester API`_ for transparent use with Provisioning Interface models.

Metadata Records (ReDBox)
-------------------------

EnMaSSe integrates with ReDBox_ using the new-alerts harvest system by:

#. Creating an XPATH mapping file between the XML export file and ReDBox_ fields.
#. Writing the Provisioning interface Metadata table to an XML file.
#. Transferring the XML metadata to the ReDBox_ server using SFTP.
#. Hitting the new-alerts URL which tells ReDBox_ to run the harvest.

Code that handles ReDBox integration in EnMaSSe
+++++++++++++++++++++++++++++++++++++++++++++++

https://github.com/jcu-eresearch/TDH-rich-data-capture/blob/master/jcudc24provisioning/scripts/create_redbox_config.py

This creates the XPATH mappings from the XML to ReDBox fields and is basically a hard-coded field-to-field mapping but it uses the model attribute names (eg. field names can change, but new/delete fields need to be remapped).

https://github.com/jcu-eresearch/TDH-rich-data-capture/blob/master/jcudc24provisioning/models/ca_model.py#LC487

to_xml() is what converts the Metadata object to XML.

https://github.com/jcu-eresearch/TDH-rich-data-capture/blob/master/jcudc24provisioning/models/project.py#LC462

The Metadata() model represents a metadata record, so the provisioning interface sets up all relevant data then just dumps it to an XML file.

How ReDBox is updated to integrate with EnMaSSe:
++++++++++++++++++++++++++++++++++++++++++++++++

https://github.com/jcu-eresearch/TDH-Research-Data-Catalogue/tree/master/src/main/config/home/harvest/enmasse-alerts

This folder contains the harvester structure:

- New records are placed directly in this folder
- The config folder contains harvester configuration and modifications:
    - enmasse-dataset-rules.py is a customised copy of the new-alerts dataset-rules.py file (ID fixes, harvesting directly to published, etc.) which uses the data found with the XMLAlertHandler and adds it to ReDBox correctly.
    - enmasse-dataset.json is the configuration file for the harvester.
    - enmasseXmlMap.json is the EnMaSSe mapping file generated from scripts->create_redbox_mapping.py.
- Processed harvests go into the ReDBox_ created .processed folder (not in repo).


https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib

This is the code base for the new-alerts system.

Also, the system-config.json file needed to be updated with the following 2 sections:

- https://github.com/jcu-eresearch/TDH-Research-Data-Catalogue/blob/master/src/main/config/home/system-config.json#LC372
- https://github.com/jcu-eresearch/TDH-Research-Data-Catalogue/blob/master/src/main/config/home/system-config.json#LC432

Ingester Platform Integration
-----------------------------

The Provisioning Interface was developed in conjunction with the Ingester API and Ingester Platform so the database models and functional concepts are very similar.

This has made the integration particularly easy as all communication with the Ingester API maps closely to Provisioning Interface database models and the main steps required are:

- Converting Provisioning Interface models to their associated `Ingester API`_ models (the Ingester API models weren’t used directly as the Provisioning Interface requires a lot of additional display information).
- Communicating many associated models at the same time (such as all datasets associated with a project).


Full integration with the `Ingester API`_ has been implemented almost transparently with the controllers->ingesterapi_wrapper.py by extending IngesterPlatformAPI to process Provisioning Interface models passed to it’s method by:

- Using a unit of work to convert the model itself as well as all child models to Ingester API models.
- Providing any mappings of slightly different functionality between the models (such as parsing data source script+parameters into a string).
- Updating `Ingester Platform`_ ID’s on the models once the unit of work has successfully been committed.

Shibboleth Authentication
-------------------------
**TODO: This section needs updating.**


Shibboleth_ is a federated single sign-on framework that provides secure and controlled authentication, and release of user attributes. Users are redirected to their home organisation identity provider (IdP), where they supply their passwords, and then organisation policies are consulted during the release of the user’s attributes.

SHIB DIAGRAM

A Shibboleth_ enabled website is referred to as a service provider (SP). The front end webserver such as IIS or Apache HTTPD, provides a number of Shibboleth end points that are used to communicate from the IdP to the SP. Once the Shibboleth_ session is established authentication and attributes can be passed to back end application servers by securing a path with Shibboleth_. The attributes can be passed as environment variables or HTTP headers, however the EnMaSSe provisioning interface only supports the use of HTTP headers. Note, this assumes that the connection between the front end web server and the back end application server is secure. In particular, direct access to the application server must be blocked to prevent fake HTTP headers being injected.

This document assumes that Shibboleth_ is already setup on the front end web server, as configurations and requirements will vary between Shibboleth_ federations. The protected path within the Provisioning Interface is /login/shibboleth. This SP will require the following attributes:

- firstName
- surname
- commonName
- email

auEduPersonSharedToken

The final attribute, auEduPersonSharedToken, is a globally unique identifier for the user, and is what is used to link the Shibboleth_ account to the local account, as well as to accounts in other repositories.

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

See `Ingester Post Processing Scripts`_ in the Ingester Platform developers guide.

Limitations & Future Work
-------------------------

**TODO: This section needs updating.**

