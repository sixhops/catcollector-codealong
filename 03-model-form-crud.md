# The New & Improved Cat Collector Code-Along, Part 3

Wonderful of you to return! In part 3 of Cat Collector we will be seeing how Django provides powerful forms that we can use to enable any CRUD operation on our data quickly and easily. These are sort of Django's alternative to providing 100% proper RESTful routing support but it does end up saving us a lot of time even if the naming conventions aren't quite RESTful.

## Model CRUD in Django

In more minimalist frameworks, the duty of creating proper RESTful routes and forms for all operations on every resource in our app falls on us - the developers. But larger frameworks like Django or Rails often provide a quick way to cut through all the boilerplate and let us just deliver a form that is already made foer showing or updating our data. Django's solution is `ModelForms` and `generic editing views` which are classes that we can use to make a form that is already connected to our models and allows the easiest possible editing of our data.

We have already worked two READ operations into our app: `read all` and `read one`. Now let's add in the ability to `create` cats, `update` cats, and `delete` cats.

## Create-a-cat

Typically this operation is an HTTP POST request that is sent to the collection URL: `/cats`. In order for any creation of a new data object to be possible via the web, we need to follow a process:

1. The browser must be sent a page with a form on it.
2. The form must be filled out and submitted by the user.
3. The form is then returned to the server via a POST with the data attached.
4. The server reads the form data, validates it, and writes it to the table.

This is a fairly complicated process to get right when we do it ourselves but Django handles most of this internally in the form classes it provides us.

Open your `main_app/views.py` file and we will add the following code:

```python
# add this line to the import at the top
from django.views.generic.edit import CreateView, UpdateView, DeleteView

class CatCreate(CreateView):
  model = Cat
  fields = '__all__'

class CatUpdate(UpdateView):
  model = Cat
  fields = ['name', 'breed', 'description', 'age']

class CatDelete(DeleteView):
  model = Cat
  success_url = '/cats'
```