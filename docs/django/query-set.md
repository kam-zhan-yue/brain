Internally, a `QuerySet` can be constructed, filtered, sliced, and generally passed around without actually hitting the database. No database activity actually occurs until you do something to evaluate the queryset.

You can evaluate a `QuerySet` in the following ways:
- Iteration. A `QuerySet` is iterable, and it executes its database query the first time you iterate over it. E.g.
```python
for e in Entry.objects.all():
	print(e.headline)
```

- Asynchronous Iteration: A `QuerySet` can also be iterated using `async for`

```python
async for e in Entry.objects.all()
	results.append(e)
```

### `select_related`

Returns a `QuerySet` that will 'follow' foreign-key relationships, selecting additional related-object data when it executes its query. This is a performance booster which results in a single more complex query but means later use of foreign-key relationships won't require database queries.

The following examples illustrate the difference between plain lookups and `select_related` lookups.

A standard lookup looks like
```python
# Hits the database.
e = Entry.objects.get(id=5)

# Hits the database again to get the related Blog object.
b = e.blog
```

A `select_related` lookup looks like
```python
# Hits the database.
e = Entry.objects.select_related("blog").get(id=5)

# Doesn't hit the database, because e.blog has been prepopulated
# in the previous query.
b = e.blog
```

You can use `select_related` with any queryset of objects:
```python
from django.utils import timezone

# Find all the blogs with entries scheduled to be published in the future.
blogs = set()

for e in Entry.objects.filter(pub_date__gt=timezone.now()).select_related("blog"):
    # Without select_related(), this would make a database query for each
    # loop iteration in order to fetch the related blog for each entry.
    blogs.add(e.blog)
```