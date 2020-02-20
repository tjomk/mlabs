---
title: "Using Wtforms With Selectfield and Fieldlist"
date: 2016-10-29T15:30:45+02:00
draft: false
---

WTForms is an amazing library that helps with the forms creation, validation, rendering. Instead of validating each field separately in your code, all you need to do is run `form.validate()`.

### SelectField

Using WTForms is straightforward for most common fields, however when the use-case gets a bit complicated, the documentation is not of much help. Recently I had to create a project with a lot of dynamic fields and validation where I used `SelectField`, `SelectMultipleField`, and `FieldList` among others. Here is the most basic usage example for creating a form:

{{< highlight python >}}
from wtforms import Form, SelectField

class SimpleForm(Form):
    user = SelectField('User', choices=[(1, 'Admin'), (2, 'Regular')]
{{< / highlight >}}

This creates a form with a single field which has two choices: `Admin` or `Regular`. Suppose we do not know what the list of choices is in the beginning since it is kept in the database and varies from project to project. We can load it dynamically then when needed:

{{< highlight python >}}
from wtforms import Form, SelectField


class SimpleForm(Form):
    user = SelectField('User')


user_form = SimpleForm()
user_form.user.choices = [(role.id, role.name) for role in get_all_roles()]
{{< / highlight >}}

Here the `get_all_roles` function returns a list of all the roles that we then iterate and build a list of tuples. Perfect! Now the selection list is dynamic. However when we render the form, the first item item will always be selected. Suppose you are an admin and editing the user role. You want to see user’s current role instead of the first default one. We need to set the default value which is specified in the `data` attribute of the field. The option html tag has usually two values that we’re interested in: the actual value that is passed to the server, and the one user sees. You need to use the value we pass to server:

{{< highlight python >}}
from wtforms import Form, SelectField


class SimpleForm(Form):
    user = SelectField('User')


user_form = SimpleForm()
user_form.user.choices = [(role.id, role.name) for role in get_all_roles()]

user_role = get_user_role()
user_form.user.data = user_role.id
{{< / highlight >}}

### FieldList

Sometimes you need to have a form where user can add fields dynamically. Suppose it’s a shopping list and one can add either a single item, or 20. In that case you need to use FieldList which is exactly what is says: a list.

{{< highlight python >}}
from wtforms import Form, FieldList, TextField


class ShoppingListForm(Form):
    items = FieldList(TextField('Item'))

shopping_form = ShoppingListForm(items=['some item'])
{{< / highlight >}}

When initializing form or adding the values dynamically after, you need to pass a list to the field. This opens up quite a lot of possibilities to create really complex forms with a lot of nesting. Here’s one example:

{{< highlight python >}}
from wtforms import Form, FieldList, FormField, TextField


class MyItem(Form):
    name = TextField('Name')
    quantity = IntegerField('Quantity')


class ShoppingListForm(Form):
    items = FieldList(FormField(MyItem))


shopping_form = ShoppingListForm(items=[{'name': 'Bread', 'quantity': 1}])
{{< / highlight >}}

Adding items dynamically works exactly the same way as with any other field. But what about getting the values from this kind of complicated form?

{{< highlight python >}}
for item in shopping_form.items:
    print('Need to buy {}x{}'.format(item.name, item.quantity))
{{< / highlight >}}

It might seem complicated in the beginning, but WTForms is a well-designed library that works really well. If there is something which lacks in the documentation you can always peek in the code.
