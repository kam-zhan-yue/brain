[See documentation here](https://docs.djangoproject.com/en/5.2/ref/models/expressions/)

Query expressions describe a value or a computation that can be used as part of an update, create, filter, order by, annotation, or aggregate. When an expression outputs a boolean value, it may be used directly in filters. There are a number of built-in expressions that can be used to help write queries. Expressions can be combined, or in some cases nested, to form more complex computations.

## Output Field

Many of the expressions support an optional `output_field` parameter. If given, Django will load the value into that field after retrieving it from the database.

`output_field` is only required when Django is unable to automatically determine the result's field type, such as complex expressions that mix field types. 


## `F()` expressions

An `F()` object represents the value of a model field, transformed value of a model field, or annotated column. It makes it possible to refer to model field values and perform database operations using them without actually having to pull them out of the database into Python memory.

Instead, Django uses the `F()` object to generate a SQL expression that describes the required operation at the database level.

The below will pull the value of `reporter.stories_filed` from the database into memory, manipulate it with Python operators, then save it back into the database.

```python
reporter = Reporters.objects.get(name="Tintin")
reporter.stories_filed += 1
reporter.save()
```

Instead, we can do

```python
from django.db.models import F

reporter = Reporters.objects.get(name="Tintin")
reporter.stories_filed = F("stories_filed") + 1
reporter.save()
```

When Django encounters an instance of `F()`, it overrides the standard Python operators to create an encapsulated SQL expression.

To access the new value saved this way, the object must be reloaded

```python
reporter = Reporters.objects.get(name="Tintin")
# Or
reporter.refresh_from_db()
```

`F()` therefore can offer performance advantages by:
- getting the database, rather than Python to do work
- reducing the number of queries some operations require

### Avoiding race conditions using `F()`

Another useful benefit of `F()` is that having the database - instead of Python - update a field's value avoids a race condition.

If two Python threads execute code, one thread could retrieve, increment and save a field's value after the other has retrieved it from the database. The value that the second thread saves will be based on the original value; the work of the first thread will be lost.

If the database is responsible for updating the field, the process is more robust: it will only ever update the field based on the value of the field in the database when the `save()` or `update()` is executed, rather than based on its value when the instance was retrieved.

To be continued...