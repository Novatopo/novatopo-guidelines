# Django Style Guide

*Based on official Django style guide*

This is a Django styling guide based on official [Django guide](https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/coding-style/)</a>

## Python style

- Use four spaces for indentation.

- Use four space hanging indentation rather than vertical alignment:

> This makes better use of space and avoids having to realign strings if the length of the first line changes.

```python
#bad
raise AttributeError('Here is a multiline error message '
                     'shortened for clarity.')
                     
#good                     
raise AttributeError(
    'Here is a multiline error message '
    'shortened for clarity.'
)
```

- Use single quotes for strings, or a double quote if the string contains a single quote. Don’t waste time doing unrelated refactoring of existing code to conform to this style.

- Use underscores, not camelCase, for variable, function and method names (i.e. **poll.get_unique_voters()**, not **poll.getUniqueVoters()**).

- Use **InitialCaps** for class names (or for factory functions that return classes).

- In docstrings, follow the style of existing docstrings and [PEP 257](https://www.python.org/dev/peps/pep-0257/).

- In tests, use **assertRaisesMessage()** and **assertWarnsMessage()** instead of **assertRaises()** and **assertWarns()** so you can check the exception or warning message. Use **assertRaisesRegex()** and **assertWarnsRegex()** only if you need regular expression matching.

- In test docstrings, state the expected behavior that each test demonstrates. Don’t include preambles such as “Tests that” or “Ensures that”.

> Reserve ticket references for obscure issues where the ticket has additional details that can’t be easily described in docstrings or comments. Include the ticket number at the end of a sentence like this:

```python
def test_foo():
    """
    A test docstring looks like this (#123456).
    """
    ...
```

## Imports

- Use [isort](https://github.com/timothycrosley/isort#readme) to automate import sorting using the guidelines below.

- Put imports in these groups: future, standard library, third-party libraries, other Django components, local Django component, try/excepts. Sort lines in each group alphabetically by the full module name. Place all **import module** statements before **from module import objects** in each section. Use absolute imports for other Django components and relative imports for local components.

- On each line, alphabetize the items with the upper case items grouped before the lower case items.

- Break long lines using parentheses and indent continuation lines by 4 spaces. Include a trailing comma after the last import and put the closing parenthesis on its own line. Use a single blank line between the last import and any module level code, and use two blank lines above the first function or class.

```python
# future
from __future__ import unicode_literals

# standard library
import json
from itertools import chain

# third-party
import bcrypt

# Django
from django.http import Http404
from django.http.response import (
    Http404, HttpResponse, HttpResponseNotAllowed, StreamingHttpResponse,
    cookie,
)

# local Django
from .models import LogEntry

# try/except
try:
    import yaml
except ImportError:
    yaml = None

CONSTANT = 'foo'


class Example:
    # ...
```

- Use convenience imports whenever available. For example, do this:

```python
#bad
from django.views.generic.base import View

#good
from django.views import View
```

## Template style

- In Django template code, put one (and only one) space between the curly brackets and the tag contents.

```python
#bad
{{foo}}

#good
{{ foo }}
```

## View style

- In Django views, the first parameter in a view function should be called **request**.

```python
#bad
def my_view(req, foo):
    # ...

#good
def my_view(request, foo):
    # ...
```

## Model style

- Field names should be all lowercase, using underscores instead of camelCase.

```python
#bad
class Person(models.Model):
    FirstName = models.CharField(max_length=20)
    Last_Name = models.CharField(max_length=40)

#good
class Person(models.Model):
    first_name = models.CharField(max_length=20)
    last_name = models.CharField(max_length=40)
```

- The **class Meta** should appear *after* the fields are defined, with a single blank line separating the fields and the class definition.

```python
#bad
class Person(models.Model):
    first_name = models.CharField(max_length=20)
    last_name = models.CharField(max_length=40)
    class Meta:
        verbose_name_plural = 'people'
        
#even worse
class Person(models.Model):
    class Meta:
        verbose_name_plural = 'people'

    first_name = models.CharField(max_length=20)
    last_name = models.CharField(max_length=40)
    
#good
class Person(models.Model):
    first_name = models.CharField(max_length=20)
    last_name = models.CharField(max_length=40)

    class Meta:
        verbose_name_plural = 'people'
```

- The order of model inner classes and standard methods should be as follows (noting that these are not all required):

  - All database fields
  - Custom manager attributes
  - **class Meta**
  - **def __str__()**
  - **def save()**
  - **def get_absolute_url()**
  * Any custom methods

- If **choices** is defined for a given model field, define each choice as a tuple of tuples, with an all-uppercase name as a class attribute on the model. Example:

```python
class MyModel(models.Model):
    DIRECTION_UP = 'U'
    DIRECTION_DOWN = 'D'
    DIRECTION_CHOICES = (
        (DIRECTION_UP, 'Up'),
        (DIRECTION_DOWN, 'Down'),
    )
```

- **related_name** should be always set and be adequate. In the majority of cases, the name of the model in plural will be just right.

```python
#bad
class Owner(models.Model):
    pass
class Item(models.Model):
    owner = models.ForeignKey(Owner, related_name='some_item_for_owner')

#good
class Owner(models.Model):
    pass
class Item(models.Model):
    owner = models.ForeignKey(Owner, related_name='items')
```

## Miscellaneous

- Mark all strings for internationalization; see the [i18n documentation](https://docs.djangoproject.com/en/2.1/topics/i18n/) for details.

- Remove **import** statements that are no longer used when you change code. [flake8](https://pypi.org/project/flake8/) will identify these imports for you. If an unused import needs to remain for backwards-compatibility, mark the end of with **# NOQA** to silence the flake8 warning.

- Do not use **FloatField** to store information about the quantity of money. Instead, use **DecimalField** for this purpose. 

- Create *whitelist* instead of *blacklist*.
 
