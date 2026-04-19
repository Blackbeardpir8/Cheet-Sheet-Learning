# Django REST Framework (DRF) — Complete Interview Notes

> **Goal:** Revise entire DRF quickly before interviews. Covers everything from basics to industry-level best practices.

---

## Table of Contents

1. [Serializers](#1-serializers)
   - [Serializer](#11-serializer)
   - [ModelSerializer](#12-modelserializer)
   - [HyperlinkedModelSerializer](#13-hyperlinkedmodelserializer)
   - [ListSerializer](#14-listserializer)
   - [BaseSerializer](#15-baseserializer)
2. [Views — APIView](#2-views--apiview)
3. [Generic Views](#3-generic-views)
4. [Mixins](#4-mixins)
5. [ViewSets](#5-viewsets)
6. [Pagination](#6-pagination)
7. [Writing Best APIs (Industry Level)](#7-writing-best-apis-industry-level)

---

# 1. Serializers

## What is a Serializer?

**Simple Definition:**
A serializer converts complex data (like Django model objects) into simple Python data types (dicts, lists) that can be rendered into JSON/XML. It also does the reverse — it validates incoming data and converts it back into Python/Django objects.

Think of it as a **translator** between your Django models and the outside world (API consumers).

```
Django Model → Serializer → JSON   (outgoing: serialization)
JSON         → Serializer → Model  (incoming: deserialization + validation)
```

**Why is it used?**
- Convert querysets/model instances → JSON for API responses
- Validate incoming request data before saving to DB
- Control exactly which fields are exposed

---

## 1.1 Serializer

The **base** serializer. You define every field manually. No magic.

### Basic Example

```python
# models.py
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.CharField(max_length=100)
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

```python
# serializers.py
from rest_framework import serializers

class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=200)
    content = serializers.CharField()
    author = serializers.CharField(max_length=100)
    published = serializers.BooleanField(default=False)
    created_at = serializers.DateTimeField(read_only=True)
```

---

### Fields — Visible vs Hidden

**Visible fields** = fields you declare in the serializer → they appear in output and accept input.

**Hidden fields** (not declared) = they don't appear in input or output at all.

```python
class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField()       # visible
    content = serializers.CharField()     # visible
    # 'created_at' not declared → completely hidden
```

---

### Field Options

#### `read_only=True`
- Field appears in **output (response)** only
- **Cannot** be sent in input (request body) — ignored if sent
- Use for: auto-generated fields like `id`, `created_at`

```python
id = serializers.IntegerField(read_only=True)
created_at = serializers.DateTimeField(read_only=True)
```

#### `write_only=True`
- Field accepted in **input (request body)** only
- **Does NOT** appear in response output
- Use for: `password`, `otp`, sensitive fields

```python
password = serializers.CharField(write_only=True)
```

#### `required=True` (default)
- Field **must** be present in input
- If missing → validation error

```python
title = serializers.CharField(required=True)   # must be sent
```

#### `required=False`
- Field is **optional** in input

```python
bio = serializers.CharField(required=False)
```

#### `default=<value>`
- If field is missing in input, this value is used
- Makes `required=False` implicitly

```python
published = serializers.BooleanField(default=False)
```

#### `allow_null=True`
- Field can accept `null` / `None` value

```python
middle_name = serializers.CharField(allow_null=True, required=False)
```

**Summary table:**

| Option | Input | Output | Notes |
|---|---|---|---|
| `read_only=True` | ❌ ignored | ✅ shown | IDs, timestamps |
| `write_only=True` | ✅ accepted | ❌ hidden | passwords |
| `required=True` | must be present | shown | default |
| `required=False` | optional | shown if present | |
| `default=x` | optional, uses x if missing | shown | |
| `allow_null=True` | can be null | shown | |

---

### Meta Class (Why Used)

In `ModelSerializer` (explained below), the `Meta` class tells the serializer **which model** to use and **which fields** to include.

```python
class Meta:
    model = Article        # which model
    fields = '__all__'     # which fields (all or list)
    # OR
    fields = ['id', 'title', 'content']
    # OR
    exclude = ['created_at']   # all fields except these
```

**Why:** Avoids re-declaring every field manually. DRF auto-generates fields from the model.

---

### Validation

DRF has **3 types** of validation:

#### Type 1: Field-Level Validation

Method name: `validate_<fieldname>(self, value)`

Runs after the field's own built-in validation. Use for custom rules on a single field.

```python
class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField()

    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters.")
        return value  # always return the value!
```

#### Type 2: Object-Level Validation

Method name: `validate(self, data)`

Runs after all field validations pass. Has access to **all fields together**. Use for cross-field validation.

```python
class EventSerializer(serializers.Serializer):
    start_date = serializers.DateField()
    end_date = serializers.DateField()

    def validate(self, data):
        if data['end_date'] < data['start_date']:
            raise serializers.ValidationError("end_date must be after start_date.")
        return data  # always return data!
```

#### Type 3: Validators (Reusable)

Validators are **functions or classes** passed to a field's `validators` argument. Reusable across serializers.

```python
from rest_framework.validators import UniqueValidator

def no_bad_words(value):
    if 'spam' in value.lower():
        raise serializers.ValidationError("No spam allowed.")

class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(validators=[no_bad_words])
    email = serializers.EmailField(
        validators=[UniqueValidator(queryset=User.objects.all())]
    )
```

---

### Validation Error Response Format

When validation fails, DRF returns a **400 Bad Request** with this format:

```json
{
    "title": ["This field is required."],
    "end_date": ["end_date must be after start_date."],
    "non_field_errors": ["Object-level errors appear here."]
}
```

- Field errors → key is the field name
- Object-level errors → `"non_field_errors"` key

---

### How to Use a Serializer (CRUD Flow)

```python
# --- Serializing (Model → JSON) ---
article = Article.objects.get(id=1)
serializer = ArticleSerializer(article)
print(serializer.data)  # OrderedDict → JSON ready

# Many objects
articles = Article.objects.all()
serializer = ArticleSerializer(articles, many=True)

# --- Deserializing (JSON → Model) ---
data = {'title': 'Hello', 'content': 'World', 'author': 'Ali'}
serializer = ArticleSerializer(data=data)

if serializer.is_valid():
    # validated_data is clean data
    print(serializer.validated_data)
    # For plain Serializer, you must implement create/update manually
else:
    print(serializer.errors)  # validation errors
```

#### `create()` and `update()` in plain Serializer

```python
class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField()
    content = serializers.CharField()
    author = serializers.CharField()

    def create(self, validated_data):
        return Article.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.content = validated_data.get('content', instance.content)
        instance.save()
        return instance
```

Then in your view:
```python
serializer.save()          # calls create()
serializer.save(instance)  # calls update()
```

---

### Tips & Tricks — Serializer

- Always `return value` from `validate_<field>` and `return data` from `validate`
- `serializer.errors` is only populated after calling `is_valid()`
- `raise_exception=True` in `is_valid()` automatically returns 400 if invalid:
  ```python
  serializer.is_valid(raise_exception=True)
  ```
- `serializer.data` → output (serialized)
- `serializer.validated_data` → clean input after validation
- `many=True` wraps multiple objects

---

## 1.2 ModelSerializer

**Simple Definition:**
`ModelSerializer` is a shortcut that **automatically** generates fields from your Django model. You don't need to declare every field manually.

**Why use it?** Saves 80% of boilerplate code. Used in 90% of real projects.

**Advantages:**
- Auto-generates fields from model
- Auto-generates `create()` and `update()` methods
- Works with `validators` from model (e.g., `unique=True`)

**Disadvantages:**
- Less control than plain `Serializer`
- Magic can be confusing for beginners

### Example

```python
# serializers.py
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author', 'published', 'created_at']
        read_only_fields = ['id', 'created_at']
        # OR: fields = '__all__'
        # OR: exclude = ['created_at']

    # You can still add custom field-level or object-level validation
    def validate_title(self, value):
        if len(value) < 3:
            raise serializers.ValidationError("Too short.")
        return value
```

### Extra Fields / Override Fields

```python
class ArticleSerializer(serializers.ModelSerializer):
    # Add a field not in the model
    word_count = serializers.SerializerMethodField()
    # Override a model field
    author = serializers.CharField(read_only=True)

    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author', 'word_count']

    def get_word_count(self, obj):
        return len(obj.content.split())
```

### `extra_kwargs` — Clean Way to Add Options

```python
class Meta:
    model = Article
    fields = '__all__'
    extra_kwargs = {
        'password': {'write_only': True},
        'created_at': {'read_only': True},
        'content': {'required': False},
    }
```

### Full CRUD View with ModelSerializer

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Article
from .serializers import ArticleSerializer

class ArticleListCreateView(APIView):
    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)


class ArticleDetailView(APIView):
    def get_object(self, pk):
        try:
            return Article.objects.get(pk=pk)
        except Article.DoesNotExist:
            return None

    def get(self, request, pk):
        article = self.get_object(pk)
        if not article:
            return Response({'error': 'Not found'}, status=status.HTTP_404_NOT_FOUND)
        serializer = ArticleSerializer(article)
        return Response(serializer.data)

    def put(self, request, pk):
        article = self.get_object(pk)
        serializer = ArticleSerializer(article, data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)

    def patch(self, request, pk):
        article = self.get_object(pk)
        serializer = ArticleSerializer(article, data=request.data, partial=True)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)

    def delete(self, request, pk):
        article = self.get_object(pk)
        article.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

```python
# urls.py
from django.urls import path
from .views import ArticleListCreateView, ArticleDetailView

urlpatterns = [
    path('articles/', ArticleListCreateView.as_view()),
    path('articles/<int:pk>/', ArticleDetailView.as_view()),
]
```

**`partial=True`** → for PATCH requests. Makes all fields optional (update only what's sent).

---

## 1.3 HyperlinkedModelSerializer

**Simple Definition:**
Same as `ModelSerializer` but uses **URLs** instead of primary keys (IDs) to represent relationships.

**Why use it?**
- More RESTful — resources identified by URLs
- Easier for API clients to navigate
- `id=3` → `url: "http://api.example.com/articles/3/"`

### Example

```python
class ArticleSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Article
        fields = ['url', 'id', 'title', 'content']
        # 'url' field is auto-added

# In views, pass request context:
serializer = ArticleSerializer(articles, many=True, context={'request': request})
```

```python
# urls.py — must use named URLs
path('articles/<int:pk>/', ArticleDetailView.as_view(), name='article-detail'),
```

**Response:**
```json
{
    "url": "http://localhost:8000/articles/1/",
    "id": 1,
    "title": "Hello World"
}
```

**When to use:**
- HATEOAS-style APIs
- When you want navigable APIs

**When NOT to use:**
- Mobile apps / simple frontends → just use `ModelSerializer` with IDs

---

## 1.4 ListSerializer

**Simple Definition:**
Handles **bulk operations** — serializing or deserializing **multiple objects** at once.

DRF creates a `ListSerializer` automatically when you pass `many=True`. You can customize it.

**Why use it?** Bulk create/update performance.

### Custom ListSerializer (Bulk Create Example)

```python
class ArticleBulkListSerializer(serializers.ListSerializer):
    def create(self, validated_data):
        articles = [Article(**item) for item in validated_data]
        return Article.objects.bulk_create(articles)


class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['title', 'content', 'author']
        list_serializer_class = ArticleBulkListSerializer
```

```python
# Usage in view:
data = [
    {'title': 'A', 'content': 'Content A', 'author': 'Ali'},
    {'title': 'B', 'content': 'Content B', 'author': 'Sara'},
]
serializer = ArticleSerializer(data=data, many=True)
serializer.is_valid(raise_exception=True)
serializer.save()  # bulk creates both
```

---

## 1.5 BaseSerializer

**Simple Definition:**
The lowest-level class. `Serializer` inherits from it. Use when you need **full custom control** over serialization logic — no auto field handling at all.

**When to use:**
- Wrapping third-party data
- Complex custom output that doesn't map to a model

```python
class CustomStatSerializer(serializers.BaseSerializer):
    def to_representation(self, instance):
        # instance can be anything — dict, object, etc.
        return {
            'total': instance['total'],
            'active': instance['active'],
            'label': 'Statistics Dashboard',
        }

# Usage:
data = {'total': 100, 'active': 75}
serializer = CustomStatSerializer(data)
print(serializer.data)
# {'total': 100, 'active': 75, 'label': 'Statistics Dashboard'}
```

**Key methods to override:**
- `to_representation(self, instance)` → for output (serialization)
- `to_internal_value(self, data)` → for input (deserialization)
- `create(self, validated_data)`
- `update(self, instance, validated_data)`

---

# 2. Views — APIView

## What is APIView?

`APIView` is the **base class** for all DRF views. It's like Django's `View` class but enhanced for APIs:
- Handles `request.data` (parses JSON automatically)
- Returns `Response` objects (not `HttpResponse`)
- Handles authentication, permissions, throttling
- Returns proper error responses

**Why use it?** Full control over your API logic. Write GET, POST, PUT, DELETE handlers manually.

### settings.py Setup

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',  # or AllowAny
    ],
}
```

### Request & Response

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class ArticleView(APIView):
    def get(self, request):
        # request.data       → parsed body (JSON, form data)
        # request.query_params → query string (?page=1)
        # request.user       → authenticated user
        # request.method     → 'GET', 'POST', etc.
        # request.headers    → HTTP headers
        # request.auth       → auth token/object

        data = {'message': 'Hello'}
        return Response(data, status=status.HTTP_200_OK)

    def post(self, request):
        print(request.data)  # {'title': 'Hello', ...}
        return Response({'created': True}, status=status.HTTP_201_CREATED)
```

### Status Codes (Most Common)

```python
from rest_framework import status

status.HTTP_200_OK            # success GET
status.HTTP_201_CREATED       # success POST
status.HTTP_204_NO_CONTENT    # success DELETE
status.HTTP_400_BAD_REQUEST   # validation error
status.HTTP_401_UNAUTHORIZED  # not authenticated
status.HTTP_403_FORBIDDEN     # authenticated but no permission
status.HTTP_404_NOT_FOUND     # resource not found
status.HTTP_500_INTERNAL_SERVER_ERROR
```

### Authentication & Permissions (Basic Intro)

**Authentication** = Who are you?
**Permission** = What are you allowed to do?

```python
from rest_framework.authentication import BasicAuthentication, TokenAuthentication
from rest_framework.permissions import IsAuthenticated, AllowAny, IsAdminUser

class ArticleView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({'user': str(request.user)})
```

**Built-in Permissions:**
- `AllowAny` — anyone (no auth required)
- `IsAuthenticated` — must be logged in
- `IsAdminUser` — must be admin/staff
- `IsAuthenticatedOrReadOnly` — read for all, write for authenticated

**Built-in Authentication:**
- `BasicAuthentication` — username/password in header
- `SessionAuthentication` — Django session cookies
- `TokenAuthentication` — `Token <token>` in header (install `rest_framework.authtoken`)

### Full APIView Example

```python
# models.py
class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)

# serializers.py
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
        read_only_fields = ['id', 'created_at']

# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework.permissions import IsAuthenticatedOrReadOnly

class ArticleListCreateView(APIView):
    permission_classes = [IsAuthenticatedOrReadOnly]

    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save(author=request.user.username)
        return Response(serializer.data, status=status.HTTP_201_CREATED)

# urls.py
urlpatterns = [
    path('articles/', ArticleListCreateView.as_view()),
]
```

**Real Request/Response:**
```
POST /articles/
Content-Type: application/json
Authorization: Token abc123

{
    "title": "My First Article",
    "content": "Hello World"
}

Response 201:
{
    "id": 1,
    "title": "My First Article",
    "content": "Hello World",
    "author": "ali",
    "created_at": "2024-01-15T10:30:00Z"
}
```

---

# 3. Generic Views

## What are Generic Views?

Generic views are **pre-built views** that handle common patterns (list, create, retrieve, update, delete). You just plug in a serializer and queryset — DRF does the rest.

**Why use them?** Eliminate repetitive boilerplate. A full CRUD view in 5 lines.

**Hierarchy:**
```
APIView
  └── GenericAPIView         ← core base (adds queryset, serializer_class, etc.)
        ├── CreateAPIView
        ├── ListAPIView
        ├── RetrieveAPIView
        ├── UpdateAPIView
        ├── DestroyAPIView
        ├── ListCreateAPIView
        ├── RetrieveUpdateAPIView
        ├── RetrieveDestroyAPIView
        └── RetrieveUpdateDestroyAPIView
```

---

## 3.1 GenericAPIView (Core Base)

Adds these attributes and methods on top of `APIView`:

```python
class GenericAPIView(APIView):
    queryset = None              # what data to work with
    serializer_class = None      # which serializer to use
    lookup_field = 'pk'          # URL lookup (default: pk)
    filter_backends = []         # filtering
    pagination_class = None      # pagination

    def get_queryset(self)       # returns queryset
    def get_serializer(self, ...)
    def get_object(self)         # returns single object (with 404)
```

Rarely used directly. Used as base for concrete views.

---

## 3.2 Concrete Generic Views

### CreateAPIView — POST only

```python
from rest_framework.generics import CreateAPIView

class ArticleCreateView(CreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticated]
```

```
POST /articles/  → creates and returns 201
```

---

### ListAPIView — GET list only

```python
from rest_framework.generics import ListAPIView

class ArticleListView(ListAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

```
GET /articles/  → returns list
```

---

### RetrieveAPIView — GET single object

```python
from rest_framework.generics import RetrieveAPIView

class ArticleDetailView(RetrieveAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

```
GET /articles/1/  → returns single article
```

---

### UpdateAPIView — PUT/PATCH only

```python
from rest_framework.generics import UpdateAPIView

class ArticleUpdateView(UpdateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

```
PUT   /articles/1/   → full update
PATCH /articles/1/   → partial update
```

---

### DestroyAPIView — DELETE only

```python
from rest_framework.generics import DestroyAPIView

class ArticleDeleteView(DestroyAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

```
DELETE /articles/1/  → returns 204 No Content
```

---

### ListCreateAPIView — GET list + POST (Most Used)

```python
from rest_framework.generics import ListCreateAPIView

class ArticleListCreateView(ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
```

```
GET  /articles/   → list
POST /articles/   → create
```

---

### RetrieveUpdateAPIView — GET + PUT/PATCH

```python
from rest_framework.generics import RetrieveUpdateAPIView

class ArticleRetrieveUpdateView(RetrieveUpdateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

---

### RetrieveDestroyAPIView — GET + DELETE

```python
from rest_framework.generics import RetrieveDestroyAPIView

class ArticleRetrieveDestroyView(RetrieveDestroyAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

---

### RetrieveUpdateDestroyAPIView — GET + PUT/PATCH + DELETE (Most Used)

```python
from rest_framework.generics import RetrieveUpdateDestroyAPIView

class ArticleDetailView(RetrieveUpdateDestroyAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
```

### Complete URLs for Generic Views

```python
# urls.py
from django.urls import path
from .views import ArticleListCreateView, ArticleDetailView

urlpatterns = [
    path('articles/', ArticleListCreateView.as_view()),
    path('articles/<int:pk>/', ArticleDetailView.as_view()),
]
```

### Override `perform_create` and `perform_update`

```python
class ArticleListCreateView(ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def perform_create(self, serializer):
        # Called by create() — add extra data before saving
        serializer.save(author=self.request.user)

    def perform_update(self, serializer):
        serializer.save(updated_by=self.request.user)
```

### Custom QuerySet Filtering

```python
class ArticleListView(ListAPIView):
    serializer_class = ArticleSerializer

    def get_queryset(self):
        # Filter based on query param: /articles/?published=true
        published = self.request.query_params.get('published')
        qs = Article.objects.all()
        if published:
            qs = qs.filter(published=True)
        return qs
```

---

# 4. Mixins

## What are Mixins?

Mixins are **small building blocks** that provide individual actions (create, list, retrieve, update, destroy). The concrete generic views are just **combinations** of `GenericAPIView + Mixins`.

**Why learn them?** To understand how generic views work internally, and to build custom combinations.

```
GenericAPIView + CreateModelMixin    = CreateAPIView
GenericAPIView + ListModelMixin      = ListAPIView
GenericAPIView + RetrieveModelMixin  = RetrieveAPIView
...
```

---

## 4.1 CreateModelMixin

Provides: `create(request, ...)` → handles POST, calls `perform_create()`

```python
def create(self, request, *args, **kwargs):
    serializer = self.get_serializer(data=request.data)
    serializer.is_valid(raise_exception=True)
    self.perform_create(serializer)
    return Response(serializer.data, status=201)
```

---

## 4.2 ListModelMixin

Provides: `list(request, ...)` → handles GET (list), supports pagination

---

## 4.3 RetrieveModelMixin

Provides: `retrieve(request, ...)` → handles GET (single), calls `get_object()`

---

## 4.4 UpdateModelMixin

Provides: `update(request, ...)` → handles PUT/PATCH, calls `perform_update()`

---

## 4.5 DestroyModelMixin

Provides: `destroy(request, ...)` → handles DELETE, calls `perform_destroy()`

---

## Custom Combination with Mixins

**Problem:** I want only List + Create + Delete (no update, no retrieve). No pre-built view exists.

**Solution:** Build your own!

```python
from rest_framework import generics, mixins

class ArticleListCreateDeleteView(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    mixins.DestroyModelMixin,
    generics.GenericAPIView
):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

---

# 5. ViewSets

## What is a ViewSet?

A ViewSet combines multiple related views into **one class**. Instead of writing separate views for list, create, detail, update, delete — write one ViewSet.

**Why use it?** Less code. Works seamlessly with `Router` to auto-generate URLs.

**Key difference from APIView/GenericAPIView:**
- ViewSet uses **actions** (`list`, `create`, `retrieve`, `update`, `destroy`) instead of HTTP methods
- Router generates URLs automatically

---

## 5.1 ViewSet (Base)

Most basic. No built-in actions. You define everything.

```python
from rest_framework.viewsets import ViewSet
from rest_framework.response import Response

class ArticleViewSet(ViewSet):
    def list(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)

    def create(self, request):
        serializer = ArticleSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=201)

    def retrieve(self, request, pk=None):
        article = Article.objects.get(pk=pk)
        serializer = ArticleSerializer(article)
        return Response(serializer.data)
```

---

## 5.2 ViewSetMixin

The internal mixin that makes ViewSets work with Routers. Not used directly.

---

## 5.3 GenericViewSet

`GenericAPIView` + `ViewSetMixin`. Has queryset/serializer support but no actions. Build custom ViewSets by adding mixins.

```python
from rest_framework.viewsets import GenericViewSet
from rest_framework import mixins

class ArticleViewSet(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    GenericViewSet
):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

---

## 5.4 ModelViewSet (Most Used)

Full CRUD in one class. All 5 actions included.

```python
from rest_framework.viewsets import ModelViewSet

class ArticleViewSet(ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
```

### Router — Auto URL Generation

```python
# urls.py
from rest_framework.routers import DefaultRouter
from .views import ArticleViewSet

router = DefaultRouter()
router.register('articles', ArticleViewSet, basename='article')

urlpatterns = router.urls
```

**Auto-generated URLs:**
```
GET    /articles/          → list
POST   /articles/          → create
GET    /articles/{pk}/     → retrieve
PUT    /articles/{pk}/     → update
PATCH  /articles/{pk}/     → partial update
DELETE /articles/{pk}/     → destroy
```

---

## 5.5 ReadOnlyModelViewSet

Only `list` and `retrieve`. No create/update/delete.

```python
from rest_framework.viewsets import ReadOnlyModelViewSet

class ArticleReadOnlyViewSet(ReadOnlyModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

```
GET /articles/      → list
GET /articles/{pk}/ → retrieve
```

---

## Custom Actions with `@action`

Add non-standard endpoints to a ViewSet:

```python
from rest_framework.decorators import action

class ArticleViewSet(ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    @action(detail=False, methods=['get'], url_path='published')
    def published_articles(self, request):
        """GET /articles/published/"""
        qs = self.get_queryset().filter(published=True)
        serializer = self.get_serializer(qs, many=True)
        return Response(serializer.data)

    @action(detail=True, methods=['post'], url_path='publish')
    def publish(self, request, pk=None):
        """POST /articles/{pk}/publish/"""
        article = self.get_object()
        article.published = True
        article.save()
        return Response({'status': 'published'})
```

- `detail=False` → acts on the collection (`/articles/published/`)
- `detail=True` → acts on a single object (`/articles/1/publish/`)

---

## ViewSet Action Mapping

| Action | HTTP Method | URL |
|---|---|---|
| `list` | GET | `/articles/` |
| `create` | POST | `/articles/` |
| `retrieve` | GET | `/articles/{pk}/` |
| `update` | PUT | `/articles/{pk}/` |
| `partial_update` | PATCH | `/articles/{pk}/` |
| `destroy` | DELETE | `/articles/{pk}/` |

---

# 6. Pagination

## What is Pagination?

Pagination splits large querysets into **pages** so you don't return thousands of records at once.

**Why use it?**
- Performance (smaller responses)
- Better UX for frontends
- Industry standard

---

## Global Pagination Settings

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

---

## 6.1 BasePagination

Abstract base class. All pagination classes inherit from it. Override `paginate_queryset()` and `get_paginated_response()` for fully custom pagination.

---

## 6.2 PageNumberPagination

**Simplest.** Client requests by page number: `?page=2`

```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class StandardPagination(PageNumberPagination):
    page_size = 10                    # items per page
    page_size_query_param = 'page_size'  # allow client to change: ?page_size=5
    max_page_size = 100               # client can't request more than this
    page_query_param = 'page'         # ?page=2
```

**Apply to a view:**
```python
class ArticleListView(ListAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    pagination_class = StandardPagination
```

**Response:**
```json
{
    "count": 50,
    "next": "http://api.example.com/articles/?page=3",
    "previous": "http://api.example.com/articles/?page=1",
    "results": [...]
}
```

---

## 6.3 LimitOffsetPagination

Client controls: `?limit=10&offset=20` (skip 20, take 10)

```python
from rest_framework.pagination import LimitOffsetPagination

class ArticlePagination(LimitOffsetPagination):
    default_limit = 10
    max_limit = 100
```

**Request:** `GET /articles/?limit=5&offset=10`
**Response:**
```json
{
    "count": 50,
    "next": "http://api.example.com/articles/?limit=5&offset=15",
    "previous": "http://api.example.com/articles/?limit=5&offset=5",
    "results": [...]
}
```

---

## 6.4 CursorPagination

Most **efficient** for large datasets. Uses an encoded cursor (position pointer) instead of page numbers. No `?page=999`.

```python
from rest_framework.pagination import CursorPagination

class ArticleCursorPagination(CursorPagination):
    page_size = 10
    ordering = '-created_at'   # must specify ordering
```

**Response:**
```json
{
    "next": "http://api.example.com/articles/?cursor=cD0yMDI0",
    "previous": null,
    "results": [...]
}
```

**When to use CursorPagination:**
- Real-time feeds (Twitter, Instagram style)
- Very large datasets
- When you need consistent pagination even with new items being inserted

**Comparison:**

| Type | URL Style | Use Case |
|---|---|---|
| PageNumber | `?page=2` | Simple blogs, admin lists |
| LimitOffset | `?limit=10&offset=20` | APIs with client-controlled paging |
| Cursor | `?cursor=abc123` | Feeds, real-time, large data |

---

# 7. Writing Best APIs (Industry Level)

---

## 7.1 Industry Standard API Response Format

Never return raw serializer data. Wrap everything in a standard envelope.

### Success Response Format

```json
{
    "success": true,
    "message": "Articles fetched successfully.",
    "data": {
        "count": 50,
        "results": [
            {
                "id": 1,
                "title": "Hello World",
                "content": "..."
            }
        ]
    }
}
```

### Error Response Format

```json
{
    "success": false,
    "message": "Validation failed.",
    "errors": {
        "title": ["This field is required."],
        "content": ["This field may not be blank."]
    }
}
```

### Custom Response Helper

```python
# utils/responses.py

def success_response(data=None, message="Success.", status_code=200):
    from rest_framework.response import Response
    return Response({
        "success": True,
        "message": message,
        "data": data
    }, status=status_code)


def error_response(errors=None, message="An error occurred.", status_code=400):
    from rest_framework.response import Response
    return Response({
        "success": False,
        "message": message,
        "errors": errors
    }, status=status_code)
```

```python
# views.py — using helpers
from utils.responses import success_response, error_response

class ArticleListCreateView(APIView):
    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return success_response(
            data={'count': articles.count(), 'results': serializer.data},
            message="Articles fetched successfully."
        )

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        if not serializer.is_valid():
            return error_response(errors=serializer.errors, message="Validation failed.")
        serializer.save()
        return success_response(data=serializer.data, message="Article created.", status_code=201)
```

---

## 7.2 Linked APIs (Nested / Relational APIs)

When models have relationships (ForeignKey, ManyToMany), you have 3 ways to represent them:

### Method 1: PrimaryKeyRelatedField (Default)

```python
# models.py
class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE, related_name='comments')
    text = models.TextField()
    author = models.CharField(max_length=100)
```

```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['id', 'article', 'text', 'author']
        # 'article' will be an integer (pk)
```

Response: `{"id": 1, "article": 5, "text": "Great!", "author": "Ali"}`

### Method 2: Nested Serializer (Full Object)

```python
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title']

class CommentSerializer(serializers.ModelSerializer):
    article = ArticleSerializer(read_only=True)     # nested object

    class Meta:
        model = Comment
        fields = ['id', 'article', 'text', 'author']
```

Response:
```json
{
    "id": 1,
    "article": {"id": 5, "title": "Hello World"},
    "text": "Great!",
    "author": "Ali"
}
```

### Method 3: Reverse Relations (Show all comments in article)

```python
class ArticleSerializer(serializers.ModelSerializer):
    comments = CommentSerializer(many=True, read_only=True)  # uses related_name='comments'

    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'comments']
```

Response:
```json
{
    "id": 1,
    "title": "Hello World",
    "comments": [
        {"id": 1, "text": "Great!", "author": "Ali"},
        {"id": 2, "text": "Nice post.", "author": "Sara"}
    ]
}
```

### Method 4: SerializerMethodField (Custom)

```python
class ArticleSerializer(serializers.ModelSerializer):
    comment_count = serializers.SerializerMethodField()
    top_comment = serializers.SerializerMethodField()

    class Meta:
        model = Article
        fields = ['id', 'title', 'comment_count', 'top_comment']

    def get_comment_count(self, obj):
        return obj.comments.count()

    def get_top_comment(self, obj):
        comment = obj.comments.first()
        if comment:
            return {'text': comment.text, 'author': comment.author}
        return None
```

---

## 7.3 API Documentation — Swagger / OpenAPI

**Why document APIs?**
- Frontend developers need to know what endpoints exist
- Auto-generate client SDKs
- Industry standard

### Setup with `drf-spectacular` (Recommended)

```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS = [
    ...
    'drf_spectacular',
]

REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'My API',
    'DESCRIPTION': 'My API description',
    'VERSION': '1.0.0',
}
```

```python
# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView, SpectacularRedocView

urlpatterns = [
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
    path('api/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]
```

Visit `http://localhost:8000/api/docs/` → interactive Swagger UI.

### Documenting Your Views

```python
from drf_spectacular.utils import extend_schema, OpenApiParameter

class ArticleListCreateView(ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    @extend_schema(
        summary="List all articles",
        description="Returns a paginated list of all published articles.",
        parameters=[
            OpenApiParameter(name='published', type=bool, description='Filter by published status'),
        ]
    )
    def get(self, request, *args, **kwargs):
        return super().get(request, *args, **kwargs)

    @extend_schema(
        summary="Create an article",
        description="Creates a new article. Requires authentication.",
    )
    def post(self, request, *args, **kwargs):
        return super().post(request, *args, **kwargs)
```

---

## 7.4 Choices for Frontend

### Define Choices in Model

```python
class Article(models.Model):
    class Status(models.TextChoices):
        DRAFT = 'draft', 'Draft'
        PUBLISHED = 'published', 'Published'
        ARCHIVED = 'archived', 'Archived'

    title = models.CharField(max_length=200)
    status = models.CharField(
        max_length=20,
        choices=Status.choices,
        default=Status.DRAFT
    )
```

### Expose Choices in API

```python
# views.py — Endpoint to return choices
class ArticleChoicesView(APIView):
    permission_classes = [AllowAny]

    def get(self, request):
        choices = {
            'status': [
                {'value': val, 'label': label}
                for val, label in Article.Status.choices
            ]
        }
        return Response(choices)
```

```
GET /articles/choices/

Response:
{
    "status": [
        {"value": "draft", "label": "Draft"},
        {"value": "published", "label": "Published"},
        {"value": "archived", "label": "Archived"}
    ]
}
```

Frontend can use this to populate dropdowns — never hardcode choices on the frontend.

---

## 7.5 URL Design (REST Naming Conventions)

### Golden Rules

1. **Use nouns, not verbs** in URLs
2. **Use plural** for collections
3. **Use lowercase** with hyphens for multi-word
4. **Nest resources** only when they belong to a parent

### Correct vs Wrong

```
✅ CORRECT                          ❌ WRONG
GET /articles/                      GET /getArticles/
POST /articles/                     POST /createArticle/
GET /articles/1/                    GET /article/1/
PUT /articles/1/                    PUT /updateArticle/1/
DELETE /articles/1/                 DELETE /deleteArticle?id=1/
GET /articles/1/comments/           GET /getArticleComments/1/
POST /articles/1/comments/          POST /addComment/
```

### Nested URLs

```python
# urls.py
urlpatterns = [
    # Articles
    path('articles/', ArticleListCreateView.as_view()),
    path('articles/<int:pk>/', ArticleDetailView.as_view()),

    # Comments nested under articles
    path('articles/<int:article_pk>/comments/', CommentListCreateView.as_view()),
    path('articles/<int:article_pk>/comments/<int:pk>/', CommentDetailView.as_view()),

    # Custom actions
    path('articles/<int:pk>/publish/', ArticlePublishView.as_view()),
    path('articles/choices/', ArticleChoicesView.as_view()),
]
```

### With Routers (Nested)

```bash
pip install drf-nested-routers
```

```python
from rest_framework_nested import routers

router = routers.DefaultRouter()
router.register('articles', ArticleViewSet, basename='article')

articles_router = routers.NestedDefaultRouter(router, 'articles', lookup='article')
articles_router.register('comments', CommentViewSet, basename='article-comments')

urlpatterns = router.urls + articles_router.urls
```

Generated URLs:
```
/articles/
/articles/{article_pk}/
/articles/{article_pk}/comments/
/articles/{article_pk}/comments/{id}/
```

### API Versioning

```python
# Option 1: URL versioning (most common)
urlpatterns = [
    path('api/v1/articles/', include('articles.urls')),
    path('api/v2/articles/', include('articles_v2.urls')),
]

# Option 2: DRF built-in versioning
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION': 'v1',
    'ALLOWED_VERSIONS': ['v1', 'v2'],
    'VERSION_PARAM': 'version',
}
```

---

## 7.6 Error Responses — Standard Structure

### Standard Error Format

```json
{
    "success": false,
    "message": "Validation failed.",
    "errors": {
        "title": ["This field is required."],
        "email": ["Enter a valid email address."]
    },
    "error_code": "VALIDATION_ERROR"
}
```

### Custom Exception Handler

```python
# utils/exceptions.py
from rest_framework.views import exception_handler
from rest_framework.response import Response

def custom_exception_handler(exc, context):
    # Call DRF's default handler first
    response = exception_handler(exc, context)

    if response is not None:
        response.data = {
            "success": False,
            "message": "An error occurred.",
            "errors": response.data,
            "status_code": response.status_code,
        }

    return response
```

```python
# settings.py
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'utils.exceptions.custom_exception_handler',
}
```

### Common Error Codes to Handle

```python
from rest_framework.exceptions import (
    ValidationError,       # 400 — invalid data
    AuthenticationFailed,  # 401 — bad credentials
    NotAuthenticated,      # 401 — not logged in
    PermissionDenied,      # 403 — no permission
    NotFound,              # 404 — not found
    MethodNotAllowed,      # 405 — wrong HTTP method
    Throttled,             # 429 — rate limited
)
```

### Custom Exception Classes

```python
# utils/exceptions.py
from rest_framework.exceptions import APIException
from rest_framework import status

class ArticleNotPublishedException(APIException):
    status_code = status.HTTP_400_BAD_REQUEST
    default_detail = "This article is not published yet."
    default_code = "article_not_published"

# Usage in view:
if not article.published:
    raise ArticleNotPublishedException()
```

---

## 7.7 Complete Industry-Level Example

### Project: Blog API

```python
# models.py
class Category(models.Model):
    name = models.CharField(max_length=100, unique=True)

    def __str__(self):
        return self.name


class Article(models.Model):
    class Status(models.TextChoices):
        DRAFT = 'draft', 'Draft'
        PUBLISHED = 'published', 'Published'
        ARCHIVED = 'archived', 'Archived'

    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    content = models.TextField()
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True)
    status = models.CharField(max_length=20, choices=Status.choices, default=Status.DRAFT)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        ordering = ['-created_at']

    def __str__(self):
        return self.title
```

```python
# serializers.py
from rest_framework import serializers
from .models import Article, Category


class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name']


class ArticleListSerializer(serializers.ModelSerializer):
    """Lightweight serializer for list view."""
    author_name = serializers.CharField(source='author.username', read_only=True)
    category_name = serializers.CharField(source='category.name', read_only=True)

    class Meta:
        model = Article
        fields = ['id', 'title', 'slug', 'author_name', 'category_name', 'status', 'created_at']


class ArticleDetailSerializer(serializers.ModelSerializer):
    """Full serializer for detail/create/update view."""
    author_name = serializers.CharField(source='author.username', read_only=True)
    category = CategorySerializer(read_only=True)
    category_id = serializers.PrimaryKeyRelatedField(
        queryset=Category.objects.all(),
        source='category',
        write_only=True
    )
    status_display = serializers.CharField(source='get_status_display', read_only=True)

    class Meta:
        model = Article
        fields = [
            'id', 'title', 'slug', 'content',
            'author_name', 'category', 'category_id',
            'status', 'status_display',
            'created_at', 'updated_at'
        ]
        read_only_fields = ['id', 'slug', 'author_name', 'created_at', 'updated_at']

    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters.")
        return value
```

```python
# views.py
from rest_framework.viewsets import ModelViewSet
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from django.utils.text import slugify

from .models import Article
from .serializers import ArticleListSerializer, ArticleDetailSerializer
from .pagination import StandardPagination


class ArticleViewSet(ModelViewSet):
    queryset = Article.objects.select_related('author', 'category').all()
    permission_classes = [IsAuthenticatedOrReadOnly]
    pagination_class = StandardPagination

    def get_serializer_class(self):
        if self.action == 'list':
            return ArticleListSerializer
        return ArticleDetailSerializer

    def perform_create(self, serializer):
        title = serializer.validated_data.get('title', '')
        slug = slugify(title)
        serializer.save(author=self.request.user, slug=slug)

    def get_queryset(self):
        qs = super().get_queryset()
        status = self.request.query_params.get('status')
        category = self.request.query_params.get('category')
        if status:
            qs = qs.filter(status=status)
        if category:
            qs = qs.filter(category_id=category)
        return qs

    @action(detail=True, methods=['post'], url_path='publish')
    def publish(self, request, pk=None):
        article = self.get_object()
        if article.author != request.user:
            return Response({'error': 'Not your article.'}, status=403)
        article.status = Article.Status.PUBLISHED
        article.save()
        return Response({'message': 'Article published successfully.'})

    @action(detail=False, methods=['get'], url_path='choices')
    def choices(self, request):
        return Response({
            'status': [{'value': v, 'label': l} for v, l in Article.Status.choices]
        })
```

```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class StandardPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    max_page_size = 100
```

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import ArticleViewSet

router = DefaultRouter()
router.register('articles', ArticleViewSet, basename='article')

urlpatterns = [
    path('api/v1/', include(router.urls)),
]
```

**Generated URLs:**
```
GET    /api/v1/articles/               → list
POST   /api/v1/articles/               → create
GET    /api/v1/articles/{pk}/          → retrieve
PUT    /api/v1/articles/{pk}/          → update
PATCH  /api/v1/articles/{pk}/          → partial update
DELETE /api/v1/articles/{pk}/          → destroy
POST   /api/v1/articles/{pk}/publish/  → custom action
GET    /api/v1/articles/choices/       → choices
```

---

# Quick Reference Cheat Sheet

## Serializer Field Options

| Option | Purpose |
|---|---|
| `read_only=True` | Output only (response), ignored in input |
| `write_only=True` | Input only (request), hidden in output |
| `required=True` | Must be in input (default) |
| `required=False` | Optional in input |
| `default=x` | Used if field not provided |
| `allow_null=True` | Allows None/null values |
| `allow_blank=True` | Allows empty string |
| `source='field_name'` | Maps to different model field |
| `validators=[...]` | List of validator functions |

## Serializer Types

| Class | When to Use |
|---|---|
| `Serializer` | Full control, complex non-model data |
| `ModelSerializer` | 90% of cases, maps to model |
| `HyperlinkedModelSerializer` | URL-based relationships |
| `ListSerializer` | Bulk operations |
| `BaseSerializer` | Lowest level, fully custom |

## View Types

| Class | Methods | Use Case |
|---|---|---|
| `APIView` | Manual | Full control |
| `ListAPIView` | GET list | Read-only list |
| `CreateAPIView` | POST | Create only |
| `RetrieveAPIView` | GET detail | Read one |
| `UpdateAPIView` | PUT/PATCH | Update only |
| `DestroyAPIView` | DELETE | Delete only |
| `ListCreateAPIView` | GET + POST | List + Create |
| `RetrieveUpdateDestroyAPIView` | GET+PUT+PATCH+DELETE | Full CRUD detail |
| `ModelViewSet` | All | Full CRUD + router |

## Pagination Types

| Class | Query Param | Best For |
|---|---|---|
| `PageNumberPagination` | `?page=2` | Simple pagination |
| `LimitOffsetPagination` | `?limit=10&offset=20` | Client-controlled |
| `CursorPagination` | `?cursor=abc` | Feeds, large data |

## HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 | OK (GET success) |
| 201 | Created (POST success) |
| 204 | No Content (DELETE success) |
| 400 | Bad Request (validation error) |
| 401 | Unauthorized (not authenticated) |
| 403 | Forbidden (no permission) |
| 404 | Not Found |
| 429 | Too Many Requests (throttled) |
| 500 | Internal Server Error |

## Interview Points

- `partial=True` in serializer → makes all fields optional (for PATCH)
- `perform_create()` → called by `CreateModelMixin.create()` → use to inject `author=request.user`
- `get_serializer_class()` → return different serializers for list vs detail
- `get_queryset()` → filter based on request params or user
- `@action(detail=True/False)` → custom ViewSet endpoints
- `select_related` / `prefetch_related` → optimize DB queries in `get_queryset()`
- `raise_exception=True` in `is_valid()` → auto 400 response
- `source=` → map serializer field to different model attribute/method
- `SerializerMethodField` → computed field, read-only
- `UniqueValidator` → enforce unique constraint in serializer
- `CursorPagination` → most performant, no COUNT query

---

*End of DRF Complete Notes — Ready for Interview Revision*
