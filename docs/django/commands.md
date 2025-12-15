[See documentation here](https://docs.djangoproject.com/en/6.0/howto/custom-management-commands/)

Applications can register their own actions with `manage.py`. Scripts can be added to a `management/commands` directory to the application. Django will register a command for each Python module in that directory whose name doesn't begin with an underscore.

Implementing the command would look like:
```python
from django.core.management.base import BaseCommand, CommandError
from polls.models import Question as Poll

class Command(BaseCommand):
    help = "Closes the specified poll for voting"

    def add_arguments(self, parser):
        parser.add_argument("poll_ids", nargs="+", type=int)

    def handle(self, *args, **options):
        for poll_id in options["poll_ids"]:
            try:
                poll = Poll.objects.get(pk=poll_id)
            except Poll.DoesNotExist:
                raise CommandError('Poll "%s" does not exist' % poll_id)

            poll.opened = False
            poll.save()

            self.stdout.write(
                self.style.SUCCESS('Successfully closed poll "%s"' % poll_id)
            )
```

> When you are using management commands and wish to provide console output, write to self.stdout and self.stderr instead of stdout directly. By using these proxies, it becomes much easier to test the custom command.

The above custom command can then be called with 

```shell
python manage.py closepoll <poll_ids>
```

## Testing Management Commands
Management commands can be tested with the `call_command` function. The output can then be redirected to a `StringIO` instance.

```python
from io import StringIO
from django.core.management import call_command
from django.test import TestCase

class ClosepollTest(TestCase):
    def test_command_output(self):
        out = StringIO()
        call_command("closepoll", poll_ids=[1], stdout=out)
        self.assertIn('Successfully closed poll "1"', out.getvalue())
```

