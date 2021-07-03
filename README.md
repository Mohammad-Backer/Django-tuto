# Django-app

This tutorial is a direct application of the tutorial in the django official site:
[Django Tutorial](https://docs.djangoproject.com/en/3.2/intro/tutorial01/)
Everything here is a summary of what can be found there, and what I deemed to be important and I added my own understanding
to it.

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

# Views

A view is a type of webpage in Django that generally serves a specific function and has a specific template. 
In Django, web pages and other content are delivered by views. Each view is represented by a Python function.
Django will choose a view by examining the URL that's requested(the part of the URL after the domain name.)
To get from a URL to a view, Django uses what are known as ‘URLconfs’. A URLconf maps URL patterns to views.

#   Templates:
        To use Django's template system, first we should create a directory called templates in the polls directory.
        Django will look for templates there. Inside the polls directory we create another directory called polls 
        (The name of the app) and in it we put the templates files such as index.html. The path to the template should be:
            polls/templates/polls/index.html
        Because of how the app_directories template loader works, we can refer to this template with Django as polls/index.html.

#       Removing hardcoded URLS in templates:

            The problem with this hardcoded, tightly-coupled approach is that it becomes challenging to change URLs on projects with a lot of templates. However, since we defined the name argument in the path() functions in the polls.urls module, we can remove a reliance on specific URL paths defined in our url configurations by using the {% url %} template tag.
                
                <a href = "/polls/{{question.id}}/">{{ question.question_text }}</a> 
                            becomes:
                <a href = "{%  url 'polls:detail' question.id  %}">{{ question.question_text }}</a>

            Where 'detail' is the specified name of the path in the polls/url.py file, and question.id is the argument that should be passed for the view function to work, and polls is the app_name defined in the polls/urls.py file.

            The importance of this method comes from the fact that if we want to change the URL of the polls detail view to something else, perhaps to something like polls/specifics/12/ instead of doing it in the template (or templates) we would change it in polls/urls.py

#   Important functions for views:
        1. HttpResponse: from the class django.http it returns an html response.
        2. loader:  - A class from django.template with the method get_template it takes the path to the template.
                    - render can then be applied on the template varibale and it takes two variables (request, context)
                    context is a dictionary, where the key of the dictionary can be used in html template to work as a variable.
        3. render: The render() function takes the request object as its first argument, a template name as its second argument and a  dictionary as its optional third argument. It returns an HttpResponse object of the given template rendered with the given context.
        4. Http404: It raises the Http404 if the object in the database can not be found.
        5. The get_object_or_404() function takes a Django model as its first argument and an arbitrary number of keyword arguments, which it passes to the get() function of the model’s manager. It raises Http404 if the object doesn’t exist.
        6. request.POST is a dictionary-like object that lets you access submitted data by key name. In this case, request.POST['choice'] returns the ID of the selected choice, as a string. request.POST values are always strings.
        7. HttpResponseRedirect: It takes a single argument: The URL to which the user will be redirected. It is good practice to always
        return an HttpResponseRedirect after successfully dealing with POST data.
        8. reverse(): This function helps avoid having to hardcode a URL in the view function. It is given the name of the view that we want to pass control to and the variable portion of the URL pattern that points to that view.

#   Generic Views:
        They are Class based views that inherits directly from the class generic. generic can be imported by using:
            from django.views import generic
        The two common generic views are :
            1. ListView
            2. DetailView
        Respectively these two views abstract the concepts of "display a list of objects" and "display a detail page for a particular
        type of object."
        Each generic view needs to know what model it will be acting upon. This is provided using the model attribute.
        The DetailView generic view expects the primary key value captured from the URL to be called "pk", so we’ve changed question_id to pk for the generic views.
        The template_name attribute is used to tell Django to use a specific template name instead of the autogenerated default template name.
        The context_object_name attribute is used to speicfy the name of the context variable used in the templates.

# Automated testing

[Everything useful is here!](https://docs.djangoproject.com/en/3.2/intro/tutorial05/)

# Static files

To add them to the django project, we should create a static directory in our app directory. We then create a polls directory and inside
we create our css file.
Then we load them in the templates by writing the following in the template file:
    
    {% load static %}

    <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">

