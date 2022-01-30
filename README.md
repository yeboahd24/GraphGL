# Django GraphQL

Instructions for setting up GraphQL in Django project.

---

[Graphene documentation](https://docs.graphene-python.org/projects/django/en/latest/)

**Table of contents:**

1. [Graphene Installation](#graphene-installation)
2. [Why GrapQL?](#why-graphql)
3. [Creating Schema](#creating-schema)

## Graphene Intallation

Install Graphene: `pip install django_graphene`

## Why GraphQL

- No more versioned APIs
- Smaller payloads
- Strictly-typed interfaces
- Better client performance
- Less time spent documenting and navigating APIs
- Legacy app support
- Better error handling

## Creating Schema

```py

import graphene
from graphene_django import DjangoObjectType

from ingredients.models import Ingredient, Category

class CategoryType(DjangoObjectType):
    class Meta:
        model = Category
        fields = ("id", "name", "ingredients")

class IngredientType(DjangoObjectType):
    class Meta:
        model = Ingredient
        fields = ("id", "name", "notes", "category")

class Query(graphene.ObjectType):
    all_ingredients = graphene.List(IngredientType)
    category_by_name = graphene.Field(CategoryType, name=graphene.String(required=True))

    def resolve_all_ingredients(root, info):
        # We can easily optimize query count in the resolve method
        return Ingredient.objects.select_related("category").all()

    def resolve_category_by_name(root, info, name):
        try:
            return Category.objects.get(name=name)
        except Category.DoesNotExist:
            return None

schema = graphene.Schema(query=Query)

```


## User Registration

After registration check your terminal for the activation key then use pass the 
key to the token fild in [account verification](#account-verification)

```py
mutation {
  register(email: "linux2@gmail.com", username: "Linux2", password1: "dominic70", password2: "dominic70") {
    success
    errors
    token
    refreshToken
  }
}

```


## Account Verification

```py
mutation{
  verifyAccount(token:"eyJ1c2VybmFtZSI6IkxpbnV4MiIsImFjdGlvbiI6ImFjdGl2YXRpb24ifQ:1nEI2e:ADhKNBWP2c3nIEMVNhfXbUgVIVB-GS64uZ2iNs0qrHk"){
    success, errors
  }
}
```


## User Query

```py
{
  users{
    edges{
      node{
        username
      }
    }
  }
}

```