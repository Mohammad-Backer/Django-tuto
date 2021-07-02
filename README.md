# Django-app

# Setup

1. $python -m django --version : To check wich version of Django is installed.
2. $django-admin startproject <name of the project> : To create the neccessary files and directories for a django project.
3. $python manage.py runserver : to start the server.
4. $python manage.py startapp <name of the app> : to create an app in the site.

In the views.py file in the polls app we can create functions that will build stuff on the site.
This view will then be needed to be mapped to a url. And to do that we will create a urls.py file in the polls directory.
The path function can help us create a specific url for each view created in our views.py file.
The next step is to point the root URLconf at the polls.urls module. To do that we go to the urls.py file in the mysite directory,
we then use both the functions path and include to connect the url patterns.

Each view is responsible for doing one of two things: returning an HttpResponse object containing the content for the requested page, or raising an exception such as Http404.

When somebody requests a page from your website – say, “/polls/34/”, Django will load the mysite.urls Python module because it’s pointed to by the ROOT_URLCONF setting. It finds the variable named urlpatterns and traverses the patterns in order. After finding the match at 'polls/', it strips off the matching text ("polls/") and sends the remaining text – "34/" – to the ‘polls.urls’ URLconf for further processing. There it matches '<int:question_id>/', resulting in a call to the detail() view.

# Database-creation

In mysite/settings.py, we can see the settings of the database in the dict DATABASES, there we can decide which engine to process 
our database. In django it is configured by default as sqlite3 since it is already installed in python. However if we want a more Scalable database we can switch to other engines such as:
    1. django.db.backends.postgresql
    2. django.db.backends.mysql

The name of the database should be the full absolute path, including the file name, of the database.
For other databases such as postgresql, mysql, ... additional settings such as HOST, USER and PASSWORD must be added.

The installed apps may need more then one table database so to do that we must run the following command to create it:
    -$ python manage.py migrate

The migrate command looks at the INSTALLED_APPS setting and creates any necessary database tables according to the database settings in your mysite/settings.py file and the database migrations shipped with the app.

----------------------MODEL CREATION---------------------------------------------

Models are the database Layout, these models will be created as classes in the models.py file in the directory of the app.

Here, each model is represented by a class that subclasses django.db.models.Model. Each model has a number of class variables, each of which represents a database field in the model.

Each field is represented by an instance of a Field class – e.g., CharField for character fields and DateTimeField for datetimes. This tells Django what type of data each field holds.

The name of each Field instance (e.g. question_text or pub_date) is the field’s name, in machine-friendly format. You’ll use this value in your Python code, and your database will use it as the column name.

Finally, note a relationship is defined, using ForeignKey. That tells Django each Choice is related to a single Question. Django supports all the common database relationships: many-to-one, many-to-many, and one-to-one.

That small bit of model code gives Django a lot of information. With it, Django is able to:

    1.Create a database schema (CREATE TABLE statements) for this app.
    2.Create a Python database-access API for accessing Question and Choice objects.

To include the app in the project we need to add a reference to its configuration class in the INSTALLED_APPS setting.
The pollsConfig class can be found in the polls/apps.py file, so its dotted path is 'polls.apps.PollsConfig'.
Now we edit the mysite/settings.py file and add the dotted path to the INSTALLED_APPS settings.

Now we run the following commands in the terminal:
    $python manage.py makemigrations polls

By running makemigrations, you’re telling Django that you’ve made some changes to your models (in this case, you’ve made new ones) and that you’d like the changes to be stored as a migration.

Now we run the following command to apply the migrations (aka the changes to the database):
    $python manage.py migrate

# admin creation

To create an admin we run the following command:
    $python manage.py createsuperuser

By using the url localhost/admin we can access to the admin page, and there we get a direct access to the database created.
Using that we can edit it by adding or removing elements from it.

But first we should tell django for which element in the database there is an admin interface and we do that by accessing the 
file polls/aadmin.py and editing it to look like this:
    from django.contrib import admin
    from .models import Question

    admin.site.register(Question)

By doing that we gain full access as an admin to the Question table in the database and we can edit it as we like.

