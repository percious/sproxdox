General Dojo Usage Information
=================================

Sprox has with it a set of widgets capable of providing more interactive
web content, using the powerful Dojo library.  By leveraging the existing
Dojo Toscawidget library, Sprox is able to create customizable, scrollable
tables on your web page.


Installation
--------------
Since Sprox does not depend on tw.dojo by default, you must first install this package::

    easy_install tw.dojo

Ajaxy Goodness
----------------
Dojo uses Asynchronous Javascript to fill it's tables.  This means that your controller
will have to support two requests.  The first request creates a wire-frame for the table,
the second will fill the table with data.  Luckily, Sprox can help you provide the proper
template frame, as well as the crud to fill out the table.

Here is a short example where we display the username, email, and display name from the example model.
First, we instantiate the Sprox filler and tablebase as we normally would, this time with
the Dojo-specific classes. ::

    from sprox.dojo.tablebase import DojoTableBase
    from sprox.dojo.fillerbase import DojoTableFiller

    class UserTable(DojoTableBase):
        __model__ = User
        __limit_fields__ = ['user_name', 'display_name', 'email_address']
        __url__ = 'data'

    user_table = UserTable(DBSession)

    class UserTableFiller(DojoTableFiller):
        __model__ = User
        __limit_fields__ = ['user_id', 'user_name', 'display_name', 'email_address']

    user_table_filler = UserTableFiller(DBSession)

Notice the new __url__ directive.  This tells the widget where we should get the data from.
Also note that we need to include the 'user_id' field.  This is because Dojo requires a unique
identifier to render it's data.  We need to tell Sprox to include that unique field.

Then, we need to add two more functions to our controller class (as in TG2).::

    @expose('tgtest.templates.users')
    def users(self, **kw):
        pylons.c.widget = user_table
        return dict()

    @expose('json')
    def data(self, **kw):
        return user_table_filler.get_value({}, **kw)

In our users template, we provide a call to the widget.  The rest of the work is done by Dojo on the
client side. ::

    ${tmpl_context.widget()}