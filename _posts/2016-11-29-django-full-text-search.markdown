---
layout: post
title:  "Full text search in Django"
date:   2016-11-28 23:00:00 -0300
categories: django python postgresql
---

This is a basic, but hopefully complete example of an implementation of full text search in Django using PostgreSQL. This can be used as an alternative to Elasticsearch with Haystack, which is a popular solution for this problem. Despite how awesome Elasticsearch is, it might be overkill for many cases. 

This requires at least Django 1.10 and it was tested on PostgreSQL 9.6. It assumes that you know your way around both technologies.

Let's say we have a simple model like this:

{% highlight python %}
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=150)
    last_name = models.CharField(max_length=150)
    address = models.CharField(max_length=150)
{% endhighlight %}

And we want to search by first_name, last_name or address.

First, make sure that ```django.contrib.postgres``` is in ```INSTALLED_APPS```.

Next, we need a simple form to submit the search terms:

{% highlight html %}
{% raw %}
<form class="navbar-form" method="GET" role="search" action="{% url 'search' %}">
    <div class="form-group">
        <input {% if query %}value="{{ query }}"{% endif %} type="text" name="q" class="form-control" placeholder="Search">
    </div>
</form>
</li>
{% endraw %}
{% endhighlight %}

And a very simple template to show the results:

{% highlight html %}
{% raw %}

{% extends "base.html" %}

<h1>Search results:</h2>
  <ul>
{% for person in persons %}
<li>
<p>{{person.first_name}}</p>
<p>{{person.last_name}}</p>
<p>{{person.address}}</p>
</li>
{% endfor %}
</ul>

{% endraw %}
{% endhighlight %}

Now, we add the url in ```urls.py```:

{% highlight python %}
    url(r'^search/', views.SearchView.as_view(), name='search'),
{% endhighlight %}

Finally, let's write the View. A simple ListView will do the trick:

{% highlight python %}
from django.contrib.postgres.search import SearchVector
from django.views.generic.list import ListView

class SearchView(ListView):

    template_name = 'results.html'
    context_object_name = 'persons'

    def search_persons(self, query):
        results = Person.objects.annotate(
            search=SearchVector('first_name',
                                'last_name',
                                'address')).
            filter(search=query)
        return results

    def get_queryset(self):
        query = self.request.GET.get('q')
        persons = self.search_persons(query)

        return persons
{% endhighlight %}

Full text search is a complex and fascinating subject, this is only a trivial example.
Django documentation on this subject is [here](https://docs.djangoproject.com/en/dev/ref/contrib/postgres/search/) and the excellent PostgreSQL docs are [here](https://www.postgresql.org/docs/current/static/textsearch.html).
