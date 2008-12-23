.. Sprox documentation master file, created by sphinx-quickstart on Mon Dec 22 23:10:30 2008.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Sprox
=================================

Sprox is a widget generation library that has a slightly different take on the problem of creating
custom forms directly from schemas.
Sprox provides an easy way to create forms for web content which are: automatically generated, easy to customize,
and validated.  Sprox also has powerful tools to help you display your content the way you want to with table
and record viewers.  Sprox also provides a way to fill your widgets, be them forms, or otherwise with customizable
data.

Installation
--------------
Sprox is installable from pypi.::

    easy_install sprox



Form Generation
--------------------
If this is a quickstarted TG project, you probably have a "User" link over there on the left side of your screen.
Here is the simplest way to create a form based on a "User" class.::

    from sprox.formbase import AddRecordForm

    class UserForm(AddRecordForm):
        __model__ = User

    user_form = UserForm(DBSession)

That form can then be passed into the tmpl_context, just like if it were a regular ToscaWidgets form.  But
you ask, "Why do you need to pass in the DBSession?"  Well, in fact the session is not required to be passed in,
but if you want Sprox to pick up the data for the drop-down menus, and multiple select fields, it needs a way
to connect to the database in order to get the appropriate data.
You might say, "Ok, but that form gives me a lot of junk I don't want, and the password field is going to have to be verified
if we wanted to say, make a registration form."  Well, remember Sprox is fully customizable.  Here is an example registration
form based on Sprox, complete with password validation.::

    from sprox.formbase import AddRecordForm
    from formencode import Schema
    from tw.forms import PasswordField, TextField

    form_validator =  Schema(chained_validators=(FieldsMatch('password',
                                                             'verify_password',
                                                             messages={'invalidNoMatch':
                                                             'Passwords do not match'}),))
    class RegistrationForm(AddRecordForm):
        __model__ = User
        __require_fields__     = ['password', 'user_name', 'email_address']
        __omit_fields__        = ['_password', 'groups', 'created', 'user_id']
        __field_order__        = ['user_name', 'email_address', 'display_name', 'password', 'verify_password']
        __base_validator__     = form_validator
        email_address          = TextField
        display_name           = TextField
        verify_password        = PasswordField('verify_password')

    registration_form = RegistrationForm(DBSession)

Notice first that we have made three fields required set the order, and omitted the fields that
we do not want in the form.  Also, we overrode the widget type for email_address and display_name (The default
was a text area).  Lastly we add a verify_password field that is not in the current schema.  Also keep in mind that
if you were to alter the schema for your database, any fields you added to your User model would also be added to this form.
If you wanted to avoid this, you would use __limit_fields__ instead of __omit_fields__.
There are many other __modifiers__ for FormBase in Sprox, you can use them to generate the forms you desire in
any number of combinations.

Table Generation
-----------------------
Most people look for things that render forms, but fail to realize that generating tabular formed data provides
almost the same challenge.  After all, in an editable form, you must retrieve the data from the database in order
to fill in the form entries.  Well, Sprox makes this easy too.  Here is some code to list out the users and their
email addresses::

    from sprox.tablebase import TableBase
    from sprox.fillerbase import TableFiller

    class UserTable(TableBase):
        __model__ = User
        __limit_fields__ = ['display_name', 'email_address']

    user_table = UserTable(DBSession)

    class UserTableFiller(TableFiller):
        __model__ = User
        __limit_fields__ = ['display_name', 'email_address']

    user_table_value = UserTableFiller(DBSession).get_value()


And your template code would look like this::

    ${user_table_form(value=user_table_value)}

Keep in mind that since the form generators are declarative, you can use mixins and other class trickery to reduce
your code further (although it is not advised to use this to fool your fellow developer).  Can you think of a way
to reduce the 14 lines of python code above to 8?

Sprox, the big picture
-------------------------
In reality, Sprox is not about making your code smaller, but making it _smarter_.  Since you have a declarative
base to work from, you can subclass a set of widgets that fits your application.  You can provide customized widget
selectors which tell Sprox which widgets to use for which fields.  Because Sprox gives you the power to customize
any part of the form at any level of abstraction, you can create your own form generation based on the requirements
for your project.

.. toctree::
   :maxdepth: 2

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

Modules
=============

.. autosummary::
    :toctree: generated/

    sprox.formbase
    sprox.tablebase
    sprox.fillerbase
    sprox.configbase
    sprox.viewbase
    sprox.entitiesbase
    sprox.widgetselector
    sprox.validatorselector
    sprox.validators
    sprox.sprockets
    sprox.metadata
    sprox.saormprovider
    sprox.iprovider
    sprox.widgets
    sprox.util

API
===========
.. toctree::
   :maxdepth: 2

   class_diagram

Useful Links
==============

Repository `http://www.bitbucket.org/percious/sprox/src/ <http://www.bitbucket.org/percious/sprox/src/>`_

Mailing List `http://groups.google.com/group/sprox <http://groups.google.com/group/sprox>`_