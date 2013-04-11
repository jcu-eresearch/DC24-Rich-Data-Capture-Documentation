=================
Developing the Interface
=================

------------------------------
Project Structure & Overview
------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Website Pages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Application Pages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

******************************
SQLAlchemy + Deform + ColanderAlchemy
******************************

******************************
ColanderAlchemy Modifications & Usage
******************************

******************************
Models
******************************

******************************
Customised Deform Widgets & Templates
******************************

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
ReDBox Integration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Ingester Platform Integration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Shibboleth Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Shibboleth is a federated single signon framework that provides 
secure and controlled authentication and user attribute release.
Users are redirected to their home organisation, where they supply
their passwords, and then organisation policies are consulted
during the release of the user's attributes.

A Shibboleth enabled website is referred to as a service provider (SP).
Shibboleth end points is provided by a front end IIS or Apache HTTPD server, 
from where authenitcation and attributes can be passed to back end 
application servers by securing a path with Shibboleth, and passing 
the attributes through in the HTTP headers. Note, this assumes that 
the connection between the front end web server and the back end
application server is secure. In particular, direct access to the application
server must be blocked to prevent fake HTTP headers being injected.

It is assumed that Shibboleth is already setup on the front end web server.
The proected end-point within the Provisioning Interface is 
*/login/shibboleth*. This end point will require the following attributes:

# firstName
# 
# email

...

------------------------------
Adding Data Source's
------------------------------

------------------------------
Custom Import Scripts
------------------------------
