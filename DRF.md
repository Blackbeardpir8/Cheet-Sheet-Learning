# Django REST Framework (DRF) — Complete Interview Notes

---

## Table of Contents
1. [Serializers](#1-serializers)
   - [Serializer](#11-serializer)
   - [ModelSerializer](#12-modelserializer)
   - [HyperlinkedModelSerializer](#13-hyperlinkedmodelserializer)
   - [ListSerializer](#14-listserializer)
   - [BaseSerializer](#15-baseserializer)
2. [Views — APIView](#2-views--apiview)
3. [Generic Views](#3-generic-views-generics)
   - [GenericAPIView](#31-genericapiview)
   - [CreateAPIView](#32-createapiview)
   - [ListAPIView](#33-listapiview)
   - [RetrieveAPIView](#34-retrieveapiview)
   - [UpdateAPIView](#35-updateapiview)
   - [DestroyAPIView](#36-destroyapiview)
   - [ListCreateAPIView](#37-listcreateapiview)
   - [RetrieveUpdateAPIView](#38-retrieveupdateapiview)
   - [RetrieveDestroyAPIView](#39-retrievedestroyapiview)
   - [RetrieveUpdateDestroyAPIView](#310-retrieveupdatedestroyapiview)
4. [Mixins](#4-mixins)
   - [CreateModelMixin](#41-createmodelmixin)
   - [ListModelMixin](#42-listmodelmixin)
   - [RetrieveModelMixin](#43-retrievemodelmixin)
   - [UpdateModelMixin](#44-updatemodelmixin)
   - [DestroyModelMixin](#45-destroymodelmixin)
5. [ViewSets](#5-viewsets)
   - [ViewSet](#51-viewset)
   - [ViewSetMixin](#52-viewsetmixin)
   - [GenericViewSet](#53-genericviewset)
   - [ModelViewSet](#54-modelviewset)
   - [ReadOnlyModelViewSet](#55-readonlymodelviewset)
6. [Pagination](#6-pagination)
   - [BasePagination](#61-basepagination)
   - [PageNumberPagination](#62-pagenumberpagination)
   - [LimitOffsetPagination](#63-limitoffsetpagination)
   - [CursorPagination](#64-cursorpagination)

---

# 1. Serializers

> **What is a Serializer?**
> A serializer converts complex Python objects (like Django models) into simple formats like JSON (for sending to a browser), and converts JSON back into Python objects (for saving to the database). Think of it as a **translator** between your database and the outside world.

---

## 1.1 Serializer

### Simple Definition
The base class for all serializers. You manually define every field you want to expose.

### Why It's Used
- When you need full control over data structure
- When your data doesn't come from a Django model (e.g., computed data, external APIs)

### Advantages
- Very flexible — you control everything
- Works with any data (not just models)

### Disadvantages
- Verbose — you must define every field by hand
- No automatic validation from model

### Tips and Tricks
- Use `validate_<fieldname>()` for field-level validation
- Use `validate()` for cross-field validation
- `serializer.data` → serialized output
- `serializer.validated_data` → cleaned input (after `.is_valid()`)

### Best Practices
- Use `Serializer` when you're NOT working with Django models
- Always call `is_valid(raise_exception=True)` instead of checking manually

### Code Example

```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    published = models.BooleanField(default=True)
```

```python
# serializers.py
from rest_framework import serializers

class BookSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=200)
    author = serializers.CharField(max_length=100)
    price = serializers.DecimalField(max_digits=6, decimal_places=2)
    published = serializers.BooleanField(default=True)

    def validate_price(self, value):
        if value <= 0:
            raise serializers.ValidationError("Price must be positive.")
        return value

    def validate(self, data):
        # Cross-field validation
        return data

    def create(self, validated_data):
        return Book.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.save()
        return instance
```

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Book
from .serializers import BookSerializer

class BookListView(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
```

```python
# urls.py
from django.urls import path
from .views import BookListView

urlpatterns = [
    path('books/', BookListView.as_view()),
]
```

**Real API Example:**
```
GET  /books/       → Returns list of books as JSON
POST /books/       → Creates a new book from JSON body
```

---

## 1.2 ModelSerializer

### Simple Definition
A shortcut version of `Serializer` that **automatically creates fields** from your Django model. You just tell it the model and which fields to include.

### Why It's Used
- 90% of the time you're building APIs for your models
- Saves you from writing repeated field definitions

### Advantages
- Auto-generates fields from model
- Auto-generates validators (e.g., unique, max_length)
- Has built-in `create()` and `update()` methods

### Disadvantages
- Less control than plain `Serializer`
- Can accidentally expose sensitive fields if you use `fields = '__all__'`

### Tips and Tricks
- Always use `fields = ['id', 'name', ...]` explicitly — avoid `'__all__'` in production
- Use `read_only_fields = ['id', 'created_at']` to prevent editing those fields
- Use `extra_kwargs` to customize field behavior without redefining them
- Nested serializers: use another serializer as a field type

### Best Practices
- Always explicitly list fields — never expose all fields blindly
- Use `depth = 1` carefully — it shows related objects but can be slow
- Override `create()` or `update()` when you need custom save logic

### Code Example

```python
# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price', 'published']
        read_only_fields = ['id']
        extra_kwargs = {
            'price': {'min_value': 0}
        }
```

**Nested Serializer Example:**
```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['id', 'name']

class BookSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)   # nested

    class Meta:
        model = Book
        fields = ['id', 'title', 'author']
```

---

## 1.3 HyperlinkedModelSerializer

### Simple Definition
Like `ModelSerializer`, but instead of returning numeric `id` fields for relationships, it returns **URLs** (hyperlinks) to related resources.

### Why It's Used
- To build truly RESTful (HATEOAS) APIs where clients navigate by URLs
- Useful when your API consumers need to know where to fetch related data

### Advantages
- Makes APIs self-documenting (clients follow links)
- Follows REST best practices

### Disadvantages
- Requires `request` in serializer context
- URLs must be named properly in `urls.py`
- Slightly more complex to set up

### Tips and Tricks
- Uses `url` field instead of `id` by default
- Requires `HyperlinkedIdentityField` for the object's own URL
- Related objects use `HyperlinkedRelatedField`

### Best Practices
- Always pass `context={'request': request}` when instantiating the serializer
- Name your URL patterns consistently (e.g., `book-detail`, `book-list`)

### Code Example

```python
# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Book
        fields = ['url', 'id', 'title', 'author', 'price']
```

```python
# views.py
class BookListView(APIView):
    def get(self, request):
        books = Book.objects.all()
        # Must pass request in context!
        serializer = BookSerializer(books, many=True, context={'request': request})
        return Response(serializer.data)
```

```python
# urls.py — names are required!
urlpatterns = [
    path('books/', BookListView.as_view(), name='book-list'),
    path('books/<int:pk>/', BookDetailView.as_view(), name='book-detail'),
]
```

**Real API Response:**
```json
{
  "url": "http://localhost:8000/books/1/",
  "id": 1,
  "title": "Django for Beginners",
  "author": "William Vincent"
}
```

---

## 1.4 ListSerializer

### Simple Definition
A special serializer used **automatically** when you pass `many=True` to any serializer. You can also subclass it to customize bulk operations (like bulk create/update).

### Why It's Used
- To handle bulk create/update operations efficiently
- To customize how lists of objects are serialized or saved

### Advantages
- Handles bulk operations cleanly
- Can be customized for complex batch operations

### Disadvantages
- Rarely needed directly — DRF uses it internally
- Can be complex to implement for bulk updates

### Tips and Tricks
- Override `create()` in `ListSerializer` for bulk inserts
- Access it via `many_init()` class method or `Meta.list_serializer_class`

### Best Practices
- Only use `ListSerializer` when you need custom bulk behavior
- For simple lists, `many=True` is enough

### Code Example

```python
# serializers.py
from rest_framework import serializers
from .models import Book

class BulkBookListSerializer(serializers.ListSerializer):
    def create(self, validated_data):
        books = [Book(**item) for item in validated_data]
        return Book.objects.bulk_create(books)

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['title', 'author', 'price']
        list_serializer_class = BulkBookListSerializer
```

```python
# Usage in a view
data = [
    {"title": "Book A", "author": "Author 1", "price": 10.00},
    {"title": "Book B", "author": "Author 2", "price": 20.00},
]
serializer = BookSerializer(data=data, many=True)
if serializer.is_valid():
    serializer.save()  # Uses bulk_create
```

---

## 1.5 BaseSerializer

### Simple Definition
The lowest-level base class for ALL serializers. Used when you need complete control from scratch (e.g., for non-standard data formats like XML, CSV, etc.).

### Why It's Used
- When you need to serialize data that doesn't fit any standard pattern
- When implementing custom serialization logic entirely

### Advantages
- Maximum flexibility
- Can serialize anything — not just dicts or models

### Disadvantages
- You must implement everything manually: `.to_representation()`, `.to_internal_value()`, `.create()`, `.update()`

### Tips and Tricks
- `to_representation(instance)` → converts Python object → primitive data (for output)
- `to_internal_value(data)` → converts raw input → validated Python data (for input)

### Best Practices
- Use only when `Serializer` and `ModelSerializer` truly don't fit your needs

### Code Example

```python
from rest_framework import serializers
from .models import Book

class SimpleBookSerializer(serializers.BaseSerializer):
    def to_representation(self, instance):
        # Convert model instance → dict
        return {
            'title': instance.title,
            'author': instance.author,
        }

    def to_internal_value(self, data):
        # Convert incoming data → validated dict
        title = data.get('title')
        if not title:
            raise serializers.ValidationError({'title': 'This field is required.'})
        return {'title': title, 'author': data.get('author', '')}

    def create(self, validated_data):
        return Book.objects.create(**validated_data)
```

---

# 2. Views — APIView

### Simple Definition
`APIView` is the **base class for all DRF views**. It's like Django's `View` class, but with added features for APIs: authentication, permission checks, throttling, content negotiation, and proper error responses.

### Why It's Used
- When you need full manual control over what happens for each HTTP method
- Best for complex views that don't map neatly to a single model

### Advantages
- Full control — you write the logic for GET, POST, PUT, DELETE yourself
- Supports all DRF features: authentication, permissions, throttling
- Good for complex business logic

### Disadvantages
- More verbose — you write everything manually
- Code can become repetitive across different views

### Tips and Tricks
- Methods map to HTTP verbs: `def get()`, `def post()`, `def put()`, `def patch()`, `def delete()`
- `request.data` → parsed request body (replaces `request.POST`)
- `request.user` → authenticated user
- `request.auth` → authentication token/session
- Return `Response()` always — it handles content negotiation

### Best Practices
- Use `APIView` when generic views don't fit your business logic
- Keep views thin — move logic to serializers or service functions
- Always return proper HTTP status codes

### Required Settings

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
        'rest_framework.permissions.IsAuthenticated',
    ],
}
```

### Code Example

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Book
from .serializers import BookSerializer

class BookListView(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = BookSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)


class BookDetailView(APIView):
    def get_object(self, pk):
        try:
            return Book.objects.get(pk=pk)
        except Book.DoesNotExist:
            return None

    def get(self, request, pk):
        book = self.get_object(pk)
        if not book:
            return Response({'error': 'Not found'}, status=status.HTTP_404_NOT_FOUND)
        serializer = BookSerializer(book)
        return Response(serializer.data)

    def put(self, request, pk):
        book = self.get_object(pk)
        serializer = BookSerializer(book, data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)

    def delete(self, request, pk):
        book = self.get_object(pk)
        book.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

```python
# urls.py
from django.urls import path
from .views import BookListView, BookDetailView

urlpatterns = [
    path('books/', BookListView.as_view()),
    path('books/<int:pk>/', BookDetailView.as_view()),
]
```

**Real API Example:**
```
GET    /books/       → List all books
POST   /books/       → Create a book
GET    /books/1/     → Get book with id=1
PUT    /books/1/     → Update book with id=1
DELETE /books/1/     → Delete book with id=1
```

---

# 3. Generic Views (Generics)

> **What are Generic Views?**
> Pre-built views that combine `APIView` with common patterns (list, create, retrieve, update, delete). They use mixins internally to avoid repetitive code.

---

## 3.1 GenericAPIView

### Simple Definition
The **base class for all generic views**. Adds model-level features to `APIView`: `queryset`, `serializer_class`, pagination, filtering.

### Why It's Used
- Foundation for all other generic views
- Use it directly when you need 1-2 operations but want generic features

### Key Attributes
- `queryset` — what records to show
- `serializer_class` — which serializer to use
- `lookup_field` — how to find a single object (default: `pk`)
- `pagination_class` — which paginator to use

### Key Methods
- `get_queryset()` — returns the queryset (override to filter per user)
- `get_object()` — returns a single object (applies permissions)
- `get_serializer()` — returns serializer instance

### Code Example

```python
from rest_framework.generics import GenericAPIView
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer

class BookView(GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request):
        queryset = self.get_queryset()
        serializer = self.get_serializer(queryset, many=True)
        return Response(serializer.data)
```

---

## 3.2 CreateAPIView

### Simple Definition
Handles **POST** requests to create a new object.

### Equivalent To
`GenericAPIView` + `CreateModelMixin`

```python
from rest_framework.generics import CreateAPIView
from .models import Book
from .serializers import BookSerializer

class BookCreateView(CreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoint:** `POST /books/` → Creates a book

---

## 3.3 ListAPIView

### Simple Definition
Handles **GET** requests to return a list of objects.

### Equivalent To
`GenericAPIView` + `ListModelMixin`

```python
from rest_framework.generics import ListAPIView
from .models import Book
from .serializers import BookSerializer

class BookListView(ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoint:** `GET /books/` → Returns all books

---

## 3.4 RetrieveAPIView

### Simple Definition
Handles **GET** requests to return a **single** object by its `pk`.

### Equivalent To
`GenericAPIView` + `RetrieveModelMixin`

```python
from rest_framework.generics import RetrieveAPIView
from .models import Book
from .serializers import BookSerializer

class BookDetailView(RetrieveAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoint:** `GET /books/1/` → Returns book with id=1

---

## 3.5 UpdateAPIView

### Simple Definition
Handles **PUT** (full update) and **PATCH** (partial update) requests.

### Equivalent To
`GenericAPIView` + `UpdateModelMixin`

```python
from rest_framework.generics import UpdateAPIView
from .models import Book
from .serializers import BookSerializer

class BookUpdateView(UpdateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoints:**
```
PUT   /books/1/   → Full update
PATCH /books/1/   → Partial update
```

---

## 3.6 DestroyAPIView

### Simple Definition
Handles **DELETE** requests to remove an object.

### Equivalent To
`GenericAPIView` + `DestroyModelMixin`

```python
from rest_framework.generics import DestroyAPIView
from .models import Book
from .serializers import BookSerializer

class BookDeleteView(DestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoint:** `DELETE /books/1/` → Deletes book with id=1

---

## 3.7 ListCreateAPIView

### Simple Definition
Handles both **GET (list)** and **POST (create)** on the same URL.

### Equivalent To
`GenericAPIView` + `ListModelMixin` + `CreateModelMixin`

```python
from rest_framework.generics import ListCreateAPIView
from .models import Book
from .serializers import BookSerializer

class BookListCreateView(ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoints:**
```
GET  /books/   → List all books
POST /books/   → Create a book
```

---

## 3.8 RetrieveUpdateAPIView

### Simple Definition
Handles **GET**, **PUT**, and **PATCH** on a single object.

```python
from rest_framework.generics import RetrieveUpdateAPIView
from .models import Book
from .serializers import BookSerializer

class BookRetrieveUpdateView(RetrieveUpdateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoints:**
```
GET   /books/1/   → Get book
PUT   /books/1/   → Full update
PATCH /books/1/   → Partial update
```

---

## 3.9 RetrieveDestroyAPIView

### Simple Definition
Handles **GET** and **DELETE** on a single object.

```python
from rest_framework.generics import RetrieveDestroyAPIView
from .models import Book
from .serializers import BookSerializer

class BookRetrieveDestroyView(RetrieveDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

---

## 3.10 RetrieveUpdateDestroyAPIView

### Simple Definition
Handles **GET**, **PUT**, **PATCH**, and **DELETE** on a single object. The **most commonly used** detail view.

```python
from rest_framework.generics import RetrieveUpdateDestroyAPIView
from .models import Book
from .serializers import BookSerializer

class BookDetailView(RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Endpoints:**
```
GET    /books/1/   → Get
PUT    /books/1/   → Full update
PATCH  /books/1/   → Partial update
DELETE /books/1/   → Delete
```

### Common URL Pattern for Generic Views

```python
# urls.py
from django.urls import path
from .views import BookListCreateView, BookDetailView

urlpatterns = [
    path('books/', BookListCreateView.as_view()),
    path('books/<int:pk>/', BookDetailView.as_view()),
]
```

### Tips for All Generic Views
- Override `get_queryset()` to filter by current user: `return Book.objects.filter(user=self.request.user)`
- Override `perform_create()` to inject extra data on save: `serializer.save(user=self.request.user)`
- Override `get_serializer_class()` to use different serializers for different methods

---

# 4. Mixins

> **What are Mixins?**
> Mixins are **small reusable classes** that provide one specific action (create, list, retrieve, update, destroy). Generic views combine these mixins with `GenericAPIView` to work automatically. You can also combine mixins yourself for custom combinations.

---

## 4.1 CreateModelMixin

### Simple Definition
Provides the `create()` method that handles POST requests, validates data, and saves it.

### Key Method
- `create(request, *args, **kwargs)` → validates and saves, returns 201

### Hook Method
- `perform_create(serializer)` → override to customize save behavior

```python
from rest_framework import mixins, generics
from .models import Book
from .serializers import BookSerializer

class BookCreateView(mixins.CreateModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)

    def perform_create(self, serializer):
        # Inject the current user automatically
        serializer.save(created_by=self.request.user)
```

---

## 4.2 ListModelMixin

### Simple Definition
Provides the `list()` method that handles GET requests and returns a list of objects.

### Key Method
- `list(request, *args, **kwargs)` → returns paginated list

```python
class BookListView(mixins.ListModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)
```

---

## 4.3 RetrieveModelMixin

### Simple Definition
Provides the `retrieve()` method that returns a single object.

### Key Method
- `retrieve(request, *args, **kwargs)` → finds by `pk` and returns it

```python
class BookDetailView(mixins.RetrieveModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)
```

---

## 4.4 UpdateModelMixin

### Simple Definition
Provides `update()` (for PUT) and `partial_update()` (for PATCH).

### Hook Method
- `perform_update(serializer)` → override to customize update

```python
class BookUpdateView(mixins.UpdateModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def patch(self, request, *args, **kwargs):
        return self.partial_update(request, *args, **kwargs)
```

---

## 4.5 DestroyModelMixin

### Simple Definition
Provides the `destroy()` method that deletes an object and returns 204 No Content.

### Hook Method
- `perform_destroy(instance)` → override to customize delete (e.g., soft delete)

```python
class BookDeleteView(mixins.DestroyModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

### Combining Multiple Mixins

```python
# Custom: List + Create + Retrieve (no update or delete)
class BookView(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    mixins.RetrieveModelMixin,
    generics.GenericAPIView
):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        if kwargs.get('pk'):
            return self.retrieve(request, *args, **kwargs)
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```

### Mixin Hook Methods Summary

| Mixin | Main Method | Hook (Override to Customize) |
|-------|------------|-------------------------------|
| CreateModelMixin | `create()` | `perform_create(serializer)` |
| ListModelMixin | `list()` | — |
| RetrieveModelMixin | `retrieve()` | — |
| UpdateModelMixin | `update()`, `partial_update()` | `perform_update(serializer)` |
| DestroyModelMixin | `destroy()` | `perform_destroy(instance)` |

---

# 5. ViewSets

> **What are ViewSets?**
> A `ViewSet` combines multiple views (list, create, retrieve, update, destroy) for the **same resource** into a single class. Instead of writing separate views for `/books/` and `/books/1/`, you write one class. The **Router** auto-generates the URLs.

---

## 5.1 ViewSet

### Simple Definition
The base class for all viewsets. It's like `APIView` but designed to work with Routers. You define custom actions (no automatic methods).

### Why It's Used
- When you need full control but want Router URL generation
- For custom actions that don't fit CRUD

```python
from rest_framework import viewsets
from rest_framework.response import Response

class BookViewSet(viewsets.ViewSet):
    def list(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk=None):
        book = Book.objects.get(pk=pk)
        serializer = BookSerializer(book)
        return Response(serializer.data)
```

---

## 5.2 ViewSetMixin

### Simple Definition
The class that provides the magic for ViewSets — it maps HTTP methods to action names (`list`, `create`, `retrieve`, `update`, `destroy`). You rarely use it directly.

### How It Works
When you register a ViewSet with a Router, `ViewSetMixin` connects:
- `GET /books/` → `list()`
- `POST /books/` → `create()`
- `GET /books/1/` → `retrieve()`
- `PUT /books/1/` → `update()`
- `DELETE /books/1/` → `destroy()`

---

## 5.3 GenericViewSet

### Simple Definition
`GenericAPIView` + `ViewSetMixin`. Adds queryset/serializer support to a ViewSet. You add mixins manually to get the methods you want.

```python
from rest_framework import viewsets, mixins

class BookGenericViewSet(
    mixins.ListModelMixin,
    mixins.RetrieveModelMixin,
    viewsets.GenericViewSet
):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    # Only GET /books/ and GET /books/1/ are available
```

---

## 5.4 ModelViewSet

### Simple Definition
The **most powerful and commonly used** viewset. It automatically provides **all 5 operations**: list, create, retrieve, update, destroy.

### Equivalent To
`GenericViewSet` + all 5 mixins

### Advantages
- Just 3 lines of code to create a full CRUD API
- Works with Router for automatic URL generation

### Disadvantages
- Exposes all operations by default — you may need to restrict some

### Tips and Tricks
- Use `http_method_names = ['get', 'post']` to restrict HTTP methods
- Use `@action` decorator for custom endpoints
- Override `get_queryset()` to filter per user
- Override `get_serializer_class()` to use different serializers per action

```python
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    # Filter by current user
    def get_queryset(self):
        return Book.objects.filter(published=True)

    # Inject user on create
    def perform_create(self, serializer):
        serializer.save(created_by=self.request.user)

    # Custom action: GET /books/1/publish/
    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        book = self.get_object()
        book.published = True
        book.save()
        return Response({'status': 'published'})

    # Custom action: GET /books/featured/
    @action(detail=False, methods=['get'])
    def featured(self, request):
        books = Book.objects.filter(featured=True)
        serializer = self.get_serializer(books, many=True)
        return Response(serializer.data)
```

### Router Setup (Required for ViewSets)

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register(r'books', BookViewSet, basename='book')

urlpatterns = [
    path('', include(router.urls)),
]
```

**Auto-generated URLs:**
```
GET    /books/           → list
POST   /books/           → create
GET    /books/1/         → retrieve
PUT    /books/1/         → update
PATCH  /books/1/         → partial_update
DELETE /books/1/         → destroy
POST   /books/1/publish/ → publish (custom action)
GET    /books/featured/  → featured (custom action)
```

---

## 5.5 ReadOnlyModelViewSet

### Simple Definition
Like `ModelViewSet` but **only** provides `list` and `retrieve` — no create, update, or delete.

### When To Use
- Public APIs where data is read-only
- Reference data (e.g., countries, categories)

```python
from rest_framework import viewsets
from .models import Book
from .serializers import BookSerializer

class BookReadOnlyViewSet(viewsets.ReadOnlyModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**Only these endpoints:**
```
GET /books/      → list
GET /books/1/    → retrieve
```

### ViewSet Comparison Table

| ViewSet | Built-in Actions | Use When |
|---------|-----------------|----------|
| `ViewSet` | None (manual) | Full custom control |
| `GenericViewSet` | None (add mixins) | Pick specific actions |
| `ModelViewSet` | All 5 CRUD | Full CRUD API |
| `ReadOnlyModelViewSet` | list, retrieve | Read-only API |

---

# 6. Pagination

> **What is Pagination?**
> Pagination splits a large list of results into smaller pages. Instead of returning 10,000 records at once, you return 20 at a time with links/info to get the next page.

---

## 6.1 BasePagination

### Simple Definition
The abstract base class for all pagination. You subclass it to build a custom paginator. You rarely use it directly.

### Required Methods to Implement
- `paginate_queryset(queryset, request, view=None)` → returns page items
- `get_paginated_response(data)` → wraps data in response with metadata

### When To Use
- Only when none of the built-in paginators fit your needs

---

## 6.2 PageNumberPagination

### Simple Definition
The most common style. Users request pages by number: `?page=1`, `?page=2`, etc.

### Advantages
- Easy to understand for API consumers
- Simple to implement

### Disadvantages
- Slow on large datasets (SQL `OFFSET` gets slower as page number increases)
- Inconsistent if data changes between page requests

### Required Settings

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

### Custom Paginator

```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class BookPagination(PageNumberPagination):
    page_size = 10                      # default page size
    page_size_query_param = 'page_size' # allow client to set size: ?page_size=5
    max_page_size = 100                 # maximum allowed
    page_query_param = 'page'          # query param: ?page=2
```

```python
# views.py — apply to a specific view
class BookListView(ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = BookPagination
```

**API Usage:**
```
GET /books/?page=1
GET /books/?page=2&page_size=5
```

**Response:**
```json
{
  "count": 100,
  "next": "http://localhost:8000/books/?page=3",
  "previous": "http://localhost:8000/books/?page=1",
  "results": [...]
}
```

---

## 6.3 LimitOffsetPagination

### Simple Definition
Clients specify how many items they want (`limit`) and where to start (`offset`). Like SQL `LIMIT` and `OFFSET`.

### Advantages
- Flexible — clients control the window
- Familiar if clients know SQL

### Disadvantages
- Still uses SQL OFFSET — slow on large datasets
- Inconsistent with changing data

### Settings

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 10,
}
```

### Custom Paginator

```python
from rest_framework.pagination import LimitOffsetPagination

class BookLimitOffsetPagination(LimitOffsetPagination):
    default_limit = 10
    max_limit = 100
    limit_query_param = 'limit'
    offset_query_param = 'offset'
```

**API Usage:**
```
GET /books/?limit=10&offset=0    → items 1-10
GET /books/?limit=10&offset=10   → items 11-20
GET /books/?limit=5&offset=20    → items 21-25
```

**Response:**
```json
{
  "count": 100,
  "next": "http://localhost:8000/books/?limit=10&offset=20",
  "previous": "http://localhost:8000/books/?limit=10&offset=0",
  "results": [...]
}
```

---

## 6.4 CursorPagination

### Simple Definition
Uses an **encrypted cursor** (a pointer to a position in the data) instead of page numbers or offsets. The most performant pagination for large datasets.

### Advantages
- **Very fast** — uses database index, no OFFSET
- **Consistent** — new records don't cause duplicates or skipped items
- Best for real-time feeds (social media, notifications)

### Disadvantages
- Cannot jump to arbitrary pages (no "go to page 5")
- Data must be ordered by a stable, unique field
- More complex to implement

### Required Settings

```python
from rest_framework.pagination import CursorPagination

class BookCursorPagination(CursorPagination):
    page_size = 10
    ordering = '-created_at'   # MUST specify ordering field (must be on the model)
    cursor_query_param = 'cursor'
```

```python
# views.py
class BookListView(ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = BookCursorPagination
```

**API Usage:**
```
GET /books/                           → first page
GET /books/?cursor=cD0yMDIzLTA5...   → next page (cursor from previous response)
```

**Response:**
```json
{
  "next": "http://localhost:8000/books/?cursor=cD0yMDI...",
  "previous": null,
  "results": [...]
}
```

### Pagination Comparison Table

| Feature | PageNumber | LimitOffset | Cursor |
|---------|-----------|-------------|--------|
| Jump to page | ✅ Yes | ✅ Yes (via offset) | ❌ No |
| Performance (large data) | ❌ Slow | ❌ Slow | ✅ Fast |
| Stable with live data | ❌ No | ❌ No | ✅ Yes |
| Ease of use | ✅ Simple | ✅ Simple | ⚠️ Complex |
| Best for | Small/medium datasets | Flexible APIs | Feeds, large datasets |

### Disabling Pagination for a Specific View
```python
class BookListView(ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = None   # override global setting
```

---

# Quick Reference — Complete Setup Example

```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    published = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

```python
# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price', 'published']
        read_only_fields = ['id']
```

```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class BookPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    max_page_size = 100
```

```python
# views.py
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer
from .pagination import BookPagination

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = BookPagination

    def perform_create(self, serializer):
        serializer.save()

    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        book = self.get_object()
        book.published = True
        book.save()
        return Response({'status': 'published'})
```

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register(r'books', BookViewSet, basename='book')

urlpatterns = [
    path('api/', include(router.urls)),
]
```

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
    'your_app',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

---

# Summary: When to Use What

## Serializers
| Class | Use When |
|-------|---------|
| `Serializer` | Non-model data, full control needed |
| `ModelSerializer` | Working with Django models (most common) |
| `HyperlinkedModelSerializer` | Building HATEOAS / hypermedia APIs |
| `ListSerializer` | Custom bulk create/update operations |
| `BaseSerializer` | Completely custom serialization (rare) |

## Views
| Class | Use When |
|-------|---------|
| `APIView` | Complex logic, full manual control |
| Generic Views | Standard CRUD with minimal code |
| `ViewSet` + `Router` | Full resource CRUD, clean URL structure |

## Generic Views Quick Pick
| Endpoint Needed | Use |
|----------------|-----|
| List + Create | `ListCreateAPIView` |
| Get + Update + Delete | `RetrieveUpdateDestroyAPIView` |
| Just List | `ListAPIView` |
| Just Create | `CreateAPIView` |

## Pagination
| Use Case | Paginator |
|---------|-----------|
| Standard website API | `PageNumberPagination` |
| Flexible client control | `LimitOffsetPagination` |
| Large dataset / real-time feed | `CursorPagination` |
