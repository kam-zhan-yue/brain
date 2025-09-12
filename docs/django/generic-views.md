[See documentation here](https://www.django-rest-framework.org/api-guide/generic-views/)

Django's generic views were developed as a shortcut for common usage patterns by taking common idioms and patterns found in view development and abstracting them so that you can quickly write common views of data without having to repeat yourself.

### Save and Deletion Hooks

The following meethods are provided by the mixin classes and provide an easy overriding of the object save or deletion behaviour.

- `perform_create(self, serializer)` - Called by `CreateModelMixin` when saving a new object instance
- `perform_update(self, serializer)` - Called by `UpdateModelMixin` when saving an existing object instance
- `perform_destroy(self, serializer)` - Called by `DestroyModelMixin` when deleting an object instance.

These hooks are particularly useful for setting attributes that are implicit in the request, but are not part of the request data. For instance, you might set an attribute on the object based on the request user, or based on a URL keyword argument.

```python
def perform_create(self, serializer):
	serializer.save(user=self.request.user)
```

These override points are also particularly useful for adding behaviour that occurs before or after saving an object, such as emailing a confirmation, or logging the update.

```python
def perform_update(self, serializer):
	instance = serializer.save()
	send_email_confirmation(user=self.request.user, modified=instance)
```

You can also use these hooks to provide additional validation, by raising a `ValidationError()`. This can be useful if you need some validation logic to apply at the point of database save.

```python
def perform_create(self, serializer):
	queryset = SignupRequest.object.filter(user=self.request.user)
	if queryset.exists();
		raise ValidationError("You have already signed up.")
	serializer.save(user=self.request.user)
```