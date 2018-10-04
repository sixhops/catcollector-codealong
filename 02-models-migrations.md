# The New & Improved Cat Collector Code-Along, Part 2

Thanks for coming back! In this installment of Cat Collector we will be connecting Django to a database and starting to work with models and the Django ORM. Remember that the Django ORM provides functions for accessing the database without using straight SQL.

## Connecting Django and Postgres

There are a few settings we need to edit in order to have Django connect to our running Postgres. We'll start with the `catcollector/settings.py` file:

```python
# catcollector/settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': ('cats'),
    }
}
```

This sets the database driver to be the one for Postgres and sets the name of the database (`cats`). We will create this database shortly. Before that, though, we will need to install another Python module to make Postgres work with Django. Open your terminal and enter this command:

```bash
pip3 install psycopg2
```

That's all we need to do for Django. Now let's use a nice psql shortcut for creating a new database. Open your terminal and run this command:

```bash
createdb cats
```

This is a way we can create the database without being inside the psql command-line interface. With the database created, we are all set to start modeling data in our app.

# Connecting a model to our view

1.  Lets create a `model` of our Cat instead of storing it hardcoded in our `views.py`. In our `main_app/models.py` file, change the code to reflect the following:

	```python
	# main_app/models.py
	from django.db import models

	class Cat(models.Model):
	    name = models.CharField(max_length=100)
	    breed = models.CharField(max_length=100)
	    description = models.CharField(max_length=250)
      age = models.IntegerField()
	```

2. Once we create or modify any model that we want to use in Django, we must run a database `migration`. A migration is a database action that makes any necessary changes to your db tables to prepare for storing specific data attributes of your models. If we create a new model, the migration is what actually creates the table in the database. If we change the structure of a model, the migration makes the changes in the database.

Migrating is a two step process: First we create a migration file with the following command in our terminal:

```bash
python3 manage.py makemigrations
```

This migration file contains all the database changes that will need to be done to implement your code changes. To execute the migration, run this command:

```bash
python3 manage.py migrate
```

Splitting it into separate steps let you review the migration before you actually run `migrate`, if necessary. Once we've made our model, made our migrations, and executed our migrations, the data is ready to use in Django. Before we get into loading it into our pages, let's play with the data using Django ORM functions that we can run in a Django command shell.

Open the `Django Interactive Shell` to play with the database for our Cats! Enter this terminal command:

```bash
python3 manage.py shell
```

In order to get access to the cat data, we must import the Cat model that we just made. We do this in the standard Python way but we can type it right into the shell:

```bash
from main_app.models import Cat
```

To see all of our Cat objects, enter this command:

```bash
Cat.objects.all()
```

Looks like we have an empty array, which means we have no data yet! Lets add some data!

```bash
c = Cat(name="Biscuit", breed='Sphinx', description='Evil looking cuddle monster. Hairless', age=2)
c.save()
```

If you call `Cat.objects.all()` again you'll see a Cat object exists now! Let's add a `__str__` method in our model. This is a function that we can add to any `class` and it will control what happens when we pass this object into a `print()` statement. In this case, we will set it up to return the cat's name attribute:

```python
# main_app/models.py
...
def __str__(self):
	return self.name
```

## Common ORM Operations

### Create One New Record

As we did above, we can create a new cat for insertion by simply using the Cat model class to instantiate a new Cat with the desired attributes:

```bash
c = Cat(name="Maki", breed='Flamepoint Siamese', description='Lazy but ornery', age=9)
```

A new Cat is initialized and is stored in the variable `c`. Now to write that data to our table, we just call the `save()` function:

```bash
c.save()
```

Because `c` was an object made from out Cat model class, Django automatically knows that when we save it we are writing it to the `cats` table in the `cats` database.

### Read All Records

We saw above how we can access all the objects in a data table. We use the name of the model and then add `.objects.all()` and this function will return all Cat objects from the table.

```bash
Cat.objects.all()
```

### Read One Record

Another very common data operation is to read one specific record from the table based on some provided value, usually an ID. Instead of the `all()` function, we can use the `get()` function:

```bash
Cat.objects.get(id=1)
```

This allows us to specify a value for one or more of the columns. Django iterates through the rows to find the records that have a matching values in those columns and returns those records.

Django's ORM isn't so bad, right? In fact, it's rather understandable and easy to use. Now let's update our `main_app/views.py` to use our models! Remember to remove your Cat class definition, we won't need that anymore. Instead we will import our Cat model:

	```python
	# main_app/views.py
	from django.shortcuts import render
	# add this line...
	from .models import Cat

	def index(request):
	    cats = Cat.objects.all()
	    return render(request, 'index.html', { 'cats':cats })
	
	# remove the cat class and list at the bottom...
	```

Reload your page and you should see a single Cat displayed from your database! You're a wizard, Harry!

![](https://media.giphy.com/media/IN8gg3Gci335S/giphy.gif)

# I am the ADMIN!

One last really really REALLY neat thing: Django comes with a built-in super user account! Let's use it!

The super user or administrator is basically the owner of the site. When you are logged in to this account, you can access the admin interface, add additional users, and manipulate data. Run this command in the terminal:

```bash
python3 manage.py createsuperuser
```

You will prompted to enter a username, email address, and a password. You are now creating a 'web master' for your site!

Now go to your webpage and head over to the `/admin` route to see an admin portal!  

Did you mess up your password? It's okay. Forgive yourself. Go back to your terminal and use this handy-dandy command:

```bash
python3 manage.py changepassword <user_name>
```

In order to mess with our Cat data in the admin interface, we need to **register** our Cat model. To do this let's alter our `main_app/admin.py` file to allow our model to be seen:

	```python
	from django.contrib import admin
	# import your models here
	from .models import Cat

	# Register your models here
	admin.site.register(Cat)
	```

Now when we go back to our admin page, we'll see a link to our Cat model.  We can add, update, and remove Cat objects at our leisure from this section. Neat!
