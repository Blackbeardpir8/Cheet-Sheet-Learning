# Django REST Framework — Complete Notes

---

## Table of Contents

1. [Serializers](#1-serializers)
   - [1.1 What is a Serializer](#11-what-is-a-serializer)
   - [1.2 Fields — Visible and Hidden](#12-fields--visible-and-hidden)
   - [1.3 read_only and write_only](#13-read_only-and-write_only)
   - [1.4 required, default, allow_null](#14-required-default-allow_null)
   - [1.5 Meta Class](#15-meta-class)
   - [1.6 Validation Types](#16-validation-types)
     - [1.6.1 Field-level Validation](#161-field-level-validation)
     - [1.6.2 Object-level Validation](#162-object-level-validation)
     - [1.6.3 validators argument](#163-validators-argument)
   - [1.7 Validation Error Response Format](#17-validation-error-response-format)
   - [1.8 ModelSerializer](#18-modelserializer)
   - [1.9 HyperlinkedModelSerializer](#19-hyperlinkedmodelserializer)
   - [1.10 ListSerializer](#110-listserializer)
   - [1.11 BaseSerializer](#111-baseserializer)
   - [1.12 Serializer Tips and Best Practices](#112-serializer-tips-and-best-practices)

2. [Views — APIView](#2-views--apiview)
   - [2.1 What is APIView](#21-what-is-apiview)
   - [2.2 Request and Response](#22-request-and-response)
   - [2.3 Status Codes](#23-status-codes)
   - [2.4 Complete APIView Example](#24-complete-apiview-example)
   - [2.5 Authentication and Permissions Intro](#25-authentication-and-permissions-intro)

3. [Generic Views](#3-generic-views)
   - [3.1 What are Generic Views](#31-what-are-generic-views)
   - [3.2 GenericAPIView — Core Base](#32-genericapiview--core-base)
   - [3.3 All Concrete Generic Views](#33-all-concrete-generic-views)
   - [3.4 Customizing Generic Views](#34-customizing-generic-views)

4. [Mixins](#4-mixins)
   - [4.1 What are Mixins](#41-what-are-mixins)
   - [4.2 All Mixins with Examples](#42-all-mixins-with-examples)

5. [ViewSets](#5-viewsets)
   - [5.1 What is a ViewSet](#51-what-is-a-viewset)
   - [5.2 ModelViewSet](#52-modelviewset)
   - [5.3 ReadOnlyModelViewSet](#53-readonlymodelviewset)
   - [5.4 ViewSet and GenericViewSet](#54-viewset-and-genericviewset)
   - [5.5 Custom Actions with @action](#55-custom-actions-with-action)
   - [5.6 ViewSet Comparison Table](#56-viewset-comparison-table)

6. [Pagination](#6-pagination)
   - [6.1 What is Pagination](#61-what-is-pagination)
   - [6.2 PageNumberPagination](#62-pagenumberpagination)
   - [6.3 LimitOffsetPagination](#63-limitoffsetpagination)
   - [6.4 CursorPagination](#64-cursorpagination)
   - [6.5 Using Pagination in Views](#65-using-pagination-in-views)

7. [Writing Best APIs — Industry Level](#7-writing-best-apis--industry-level)
   - [7.1 Standard API Response Format](#71-standard-api-response-format)
   - [7.2 Custom Exception Handler](#72-custom-exception-handler)
   - [7.3 URL Design — REST Naming Conventions](#73-url-design--rest-naming-conventions)
   - [7.4 Exposing Choices to Frontend](#74-exposing-choices-to-frontend)
   - [7.5 API Documentation with Swagger](#75-api-documentation-with-swagger)
   - [7.6 Linked and Nested APIs](#76-linked-and-nested-apis)
   - [7.7 Error Response Standards](#77-error-response-standards)

---

## 1. Serializers

### 1.1 What is a Serializer

A serializer is a translator between Python objects/Django models and JSON.

- **Serialization** → Python object → JSON (outgoing data for GET)
- **Deserialization** → JSON → Python object with validation (incoming data for POST/PUT)

**Why use it?**
- APIs communicate in JSON, not Python objects
- Serializers handle validation automatically
- They control exactly which fields are exposed

**Advantages:**
- Automatic validation
- Clean data conversion
- Reusable across views
- DRY principle

**Disadvantages:**
- Can get complex with deeply nested data
- Performance overhead for large querysets (mitigate with `select_related`, `prefetch_related`)

---

### 1.2 Fields — Visible and Hidden

Fields declared in the serializer appear in the API response.
Fields NOT declared are hidden from the API.

```python
from rest_framework import serializers

class BookSerializer(serializers.Serializer):
    title = serializers.CharField()                 # visible
    author = serializers.CharField()                # visible
    price = serializers.DecimalField(max_digits=6, decimal_places=2)  # visible
    # internal_code is NOT declared = hidden from API
```

**Common field types:**

| Field | Use for |
|---|---|
| `CharField` | strings |
| `IntegerField` | integers |
| `FloatField` | floats |
| `DecimalField` | precise decimals |
| `BooleanField` | true/false |
| `DateField` | dates |
| `DateTimeField` | datetimes |
| `EmailField` | email strings with validation |
| `URLField` | URLs with validation |
| `SerializerMethodField` | computed/custom values |

---

### 1.3 read_only and write_only

| Option | Meaning | Appears in GET | Accepted in POST/PUT |
|---|---|---|---|
| `read_only=True` | Only for output | Yes | No (ignored) |
| `write_only=True` | Only for input | No | Yes |
| (neither) | Both directions | Yes | Yes |

```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'password', 'email']
        extra_kwargs = {
            'id': {'read_only': True},          # auto-set by DB
            'password': {'write_only': True},   # never expose password
        }
```

**Tips:**
- `id`, `created_at`, `updated_at` → always `read_only=True`
- `password`, `token`, `secret_key` → always `write_only=True`

---

### 1.4 required, default, allow_null

| Option | Default | Meaning |
|---|---|---|
| `required` | `True` | Field must be present in input |
| `required=False` | — | Field is optional |
| `default=value` | — | Used when field is absent from input |
| `allow_null=True` | `False` | Field can be `null` / `None` |
| `allow_blank=True` | `False` | CharField can be empty string |

```python
class BookSerializer(serializers.Serializer):
    title = serializers.CharField()                          # required
    subtitle = serializers.CharField(required=False)         # optional
    rating = serializers.FloatField(default=0.0)             # defaults to 0.0 if absent
    deleted_at = serializers.DateTimeField(allow_null=True)  # can be null
    bio = serializers.CharField(allow_blank=True)            # can be ""
```

---

### 1.5 Meta Class

The `Meta` class is the configuration block inside `ModelSerializer`.
It tells DRF which model to use and which fields to expose.

```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book                                     # Django model to use
        fields = ['id', 'title', 'author', 'price']     # explicit list
        # OR
        fields = '__all__'                               # all model fields
        # OR
        exclude = ['internal_code', 'admin_notes']      # all except these

        read_only_fields = ['id', 'created_at']         # mark as read-only
        
        extra_kwargs = {
            'password': {'write_only': True},
            'email': {'required': False}
        }
```

**Why use `extra_kwargs` instead of redeclaring fields?**
- Cleaner — you don't repeat field declarations
- Less code, same result

---

### 1.6 Validation Types

#### 1.6.1 Field-level Validation

Validates a single field. Method name must be `validate_<fieldname>`.
Called automatically when `.is_valid()` is called.

```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['title', 'price']

    def validate_price(self, value):
        if value <= 0:
            raise serializers.ValidationError("Price must be a positive number.")
        return value

    def validate_title(self, value):
        if len(value) < 3:
            raise serializers.ValidationError("Title must be at least 3 characters.")
        return value.strip()   # clean and return
```

#### 1.6.2 Object-level Validation

Validates multiple fields together (cross-field rules).
Use the `validate()` method.

```python
    def validate(self, data):
        if data.get('discount_price') and data['discount_price'] >= data['price']:
            raise serializers.ValidationError(
                "Discount price must be less than the original price."
            )
        if data.get('publish_date') and data.get('expiry_date'):
            if data['publish_date'] >= data['expiry_date']:
                raise serializers.ValidationError(
                    "Expiry date must be after publish date."
                )
        return data
```

#### 1.6.3 validators argument

Reusable validator functions attached to a field.

```python
from rest_framework.validators import UniqueValidator

def no_special_chars(value):
    forbidden = ['@', '#', '$', '%']
    for char in forbidden:
        if char in value:
            raise serializers.ValidationError(f"Character '{char}' is not allowed.")

class BookSerializer(serializers.ModelSerializer):
    title = serializers.CharField(
        validators=[
            no_special_chars,
            UniqueValidator(queryset=Book.objects.all())  # built-in unique validator
        ]
    )
    class Meta:
        model = Book
        fields = ['title', 'price']
```

**Built-in DRF validators:**
- `UniqueValidator` — ensure field is unique in DB
- `UniqueTogetherValidator` — ensure combination of fields is unique
- `UniqueForDateValidator` — unique within a date range

---

### 1.7 Validation Error Response Format

When `.is_valid()` fails, DRF returns `400 Bad Request` with:

**Field-level errors:**
```json
{
    "title": ["This field is required."],
    "price": ["Price must be a positive number."]
}
```

**Object-level errors (non_field_errors):**
```json
{
    "non_field_errors": ["Discount price must be less than original price."]
}
```

**Multiple errors on one field:**
```json
{
    "email": [
        "Enter a valid email address.",
        "This email already exists."
    ]
}
```

---

### 1.8 ModelSerializer

`ModelSerializer` auto-creates fields, validators, `create()`, and `update()` from the model.

**Plain `Serializer` vs `ModelSerializer`:**

```python
# Without ModelSerializer — manual, verbose
class BookSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(max_length=200)
    author = serializers.CharField(max_length=100)
    price = serializers.DecimalField(max_digits=6, decimal_places=2)
    created_at = serializers.DateTimeField(read_only=True)

    def create(self, validated_data):
        return Book.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.author = validated_data.get('author', instance.author)
        instance.price = validated_data.get('price', instance.price)
        instance.save()
        return instance

# With ModelSerializer — clean, auto-generated
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price', 'created_at']
        read_only_fields = ['id', 'created_at']
```

**Advantages of ModelSerializer:**
- Auto-generates all field types from the model
- Auto-creates `create()` and `update()`
- Auto-adds validators like `unique`, `max_length`

**Disadvantages:**
- Less control over complex create/update logic (but you can always override)

**Overriding create() and update():**

```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price', 'tags']

    def create(self, validated_data):
        tags = validated_data.pop('tags', [])
        book = Book.objects.create(**validated_data)
        book.tags.set(tags)   # ManyToMany
        return book

    def update(self, instance, validated_data):
        tags = validated_data.pop('tags', None)
        for attr, value in validated_data.items():
            setattr(instance, attr, value)
        if tags is not None:
            instance.tags.set(tags)
        instance.save()
        return instance
```

---

### 1.9 HyperlinkedModelSerializer

Returns URLs instead of IDs for related objects and self-reference.

```python
class BookSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Book
        fields = ['url', 'title', 'author', 'price']
```

**Response:**
```json
{
    "url": "http://localhost:8000/api/books/3/",
    "title": "Django for Beginners",
    "author": "William Vincent",
    "price": "29.99"
}
```

**Setup requirement:** View names must follow the `<model>-detail` pattern for auto URL resolution.

```python
# urls.py
urlpatterns = [
    path('books/<int:pk>/', BookDetailView.as_view(), name='book-detail'),
]
```

**When to use:** HATEOAS-style APIs. Less common; most teams prefer ID-based responses.

---

### 1.10 ListSerializer

Handles serialization/deserialization of multiple objects (lists).
DRF auto-creates one when you use `many=True`.

```python
# This automatically creates a ListSerializer
serializer = BookSerializer(Book.objects.all(), many=True)
serializer.data  # list of dicts
```

**Custom ListSerializer for bulk operations:**

```python
class BookListSerializer(serializers.ListSerializer):
    def create(self, validated_data):
        books = [Book(**item) for item in validated_data]
        return Book.objects.bulk_create(books)

    def update(self, instance, validated_data):
        # custom bulk update logic
        book_map = {book.id: book for book in instance}
        for item in validated_data:
            book = book_map.get(item['id'])
            if book:
                for attr, value in item.items():
                    setattr(book, attr, value)
                book.save()
        return instance

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'price']
        list_serializer_class = BookListSerializer  # plug in custom list serializer
```

---

### 1.11 BaseSerializer

The lowest-level serializer class. Complete manual control.
Use for non-model data, external APIs, or custom formats.

```python
class CustomDataSerializer(serializers.BaseSerializer):

    def to_representation(self, instance):
        """Python object → JSON (used in GET/output)"""
        return {
            'name': instance.name.upper(),
            'score': round(instance.score, 2),
            'rank': f"#{instance.rank}"
        }

    def to_internal_value(self, data):
        """JSON → validated Python dict (used in POST/PUT input)"""
        if 'name' not in data:
            raise serializers.ValidationError({'name': 'This field is required.'})
        if not isinstance(data.get('score'), (int, float)):
            raise serializers.ValidationError({'score': 'Must be a number.'})
        return {
            'name': data['name'],
            'score': float(data['score'])
        }

    def create(self, validated_data):
        return MyCustomObject(**validated_data)

    def update(self, instance, validated_data):
        for attr, value in validated_data.items():
            setattr(instance, attr, value)
        return instance
```

---

### 1.12 Serializer Tips and Best Practices

- Use `ModelSerializer` for model-backed data; `Serializer` for custom/non-model data
- Always use `read_only_fields` in `Meta` instead of field-by-field `read_only=True`
- Use `extra_kwargs` to add options without redeclaring fields
- Never expose sensitive fields — always mark `password`, `token` as `write_only=True`
- Use `SerializerMethodField` for computed values:

```python
class BookSerializer(serializers.ModelSerializer):
    full_info = serializers.SerializerMethodField()

    class Meta:
        model = Book
        fields = ['id', 'title', 'full_info']

    def get_full_info(self, obj):
        return f"{obj.title} by {obj.author} — ${obj.price}"
```

- When you expect partial updates (PATCH), use `partial=True`:

```python
serializer = BookSerializer(book, data=request.data, partial=True)
```

- Always call `.is_valid(raise_exception=True)` in production — raises `ValidationError` automatically and returns 400:

```python
serializer.is_valid(raise_exception=True)   # auto 400 on failure
serializer.save()
```

---

## 2. Views — APIView

### 2.1 What is APIView

`APIView` is the base class for all DRF views. It extends Django's `View` class and adds:
- DRF's `Request` wrapper (handles JSON, form data, multipart)
- DRF's `Response` (auto-renders to JSON)
- Authentication and permission checking
- Content negotiation
- Throttling

**Why use it?**
Gives you full control over request handling. You write `get()`, `post()`, `put()`, `patch()`, `delete()` methods manually.

**Advantages:**
- Full flexibility
- Explicit control over every action

**Disadvantages:**
- Verbose for standard CRUD
- Need to write a lot of boilerplate (use Generic Views for CRUD)

---

### 2.2 Request and Response

**DRF Request** wraps `HttpRequest`:

```python
request.data           # parsed body (works for JSON, form data, multipart)
request.query_params   # URL query string dict (?search=python → {'search': 'python'})
request.user           # authenticated User object
request.auth           # auth token / credentials
request.method         # 'GET', 'POST', 'PUT', 'PATCH', 'DELETE'
request.content_type   # content type of request
```

**DRF Response:**

```python
from rest_framework.response import Response

return Response(data)                              # 200 OK
return Response(data, status=201)                  # 201 Created
return Response({'error': 'msg'}, status=400)      # 400 Bad Request
return Response(status=204)                        # 204 No Content (delete)
```

---

### 2.3 Status Codes

Always use `status` constants — never hardcode numbers:

```python
from rest_framework import status

# 2xx Success
status.HTTP_200_OK               # GET success
status.HTTP_201_CREATED          # POST success (created)
status.HTTP_204_NO_CONTENT       # DELETE success

# 4xx Client errors
status.HTTP_400_BAD_REQUEST      # validation failed
status.HTTP_401_UNAUTHORIZED     # not logged in
status.HTTP_403_FORBIDDEN        # logged in but no permission
status.HTTP_404_NOT_FOUND        # resource not found
status.HTTP_405_METHOD_NOT_ALLOWED
status.HTTP_409_CONFLICT

# 5xx Server errors
status.HTTP_500_INTERNAL_SERVER_ERROR
```

---

### 2.4 Complete APIView Example

```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title


# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price', 'created_at']
        read_only_fields = ['id', 'created_at']


# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Book
from .serializers import BookSerializer


class BookListView(APIView):
    """GET all books, POST create a book"""

    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class BookDetailView(APIView):
    """GET, PUT, PATCH, DELETE a single book"""

    def get_object(self, pk):
        try:
            return Book.objects.get(pk=pk)
        except Book.DoesNotExist:
            return None

    def get(self, request, pk):
        book = self.get_object(pk)
        if book is None:
            return Response({'error': 'Book not found.'}, status=status.HTTP_404_NOT_FOUND)
        serializer = BookSerializer(book)
        return Response(serializer.data)

    def put(self, request, pk):
        book = self.get_object(pk)
        if book is None:
            return Response({'error': 'Book not found.'}, status=status.HTTP_404_NOT_FOUND)
        serializer = BookSerializer(book, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def patch(self, request, pk):
        book = self.get_object(pk)
        if book is None:
            return Response({'error': 'Book not found.'}, status=status.HTTP_404_NOT_FOUND)
        serializer = BookSerializer(book, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        book = self.get_object(pk)
        if book is None:
            return Response({'error': 'Book not found.'}, status=status.HTTP_404_NOT_FOUND)
        book.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)


# urls.py
from django.urls import path
from .views import BookListView, BookDetailView

urlpatterns = [
    path('books/', BookListView.as_view(), name='book-list'),
    path('books/<int:pk>/', BookDetailView.as_view(), name='book-detail'),
]
```

---

### 2.5 Authentication and Permissions Intro

**Per-view settings:**

```python
from rest_framework.permissions import IsAuthenticated, AllowAny, IsAdminUser
from rest_framework.authentication import SessionAuthentication, BasicAuthentication, TokenAuthentication

class BookListView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated]

    def get(self, request):
        ...
```

**Global settings (settings.py):**

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
```

**Built-in permission classes:**

| Class | Meaning |
|---|---|
| `AllowAny` | Everyone can access |
| `IsAuthenticated` | Must be logged in |
| `IsAdminUser` | Must be admin/staff |
| `IsAuthenticatedOrReadOnly` | Anyone can read, only authenticated can write |

---

## 3. Generic Views

### 3.1 What are Generic Views

Generic views are pre-built view classes that combine `GenericAPIView` with mixins.
They handle standard CRUD patterns in very few lines of code.

**Class hierarchy:**
```
APIView
  └── GenericAPIView
        ├── CreateAPIView           (POST)
        ├── ListAPIView             (GET list)
        ├── RetrieveAPIView         (GET detail)
        ├── UpdateAPIView           (PUT/PATCH)
        ├── DestroyAPIView          (DELETE)
        ├── ListCreateAPIView       (GET + POST)
        ├── RetrieveUpdateAPIView   (GET + PUT/PATCH)
        ├── RetrieveDestroyAPIView  (GET + DELETE)
        └── RetrieveUpdateDestroyAPIView (GET + PUT/PATCH + DELETE)
```

**Advantages:**
- Minimal code for standard CRUD
- Auto-handles validation, 404, etc.

**Disadvantages:**
- Less explicit than APIView (can feel magic)
- Customization requires knowing which methods to override

---

### 3.2 GenericAPIView — Core Base

Extends `APIView` with:
- `queryset` — define the dataset
- `serializer_class` — which serializer to use
- `get_object()` — fetch one object by pk (raises 404 automatically)
- `get_queryset()` — fetch the queryset (override for filtering)
- `get_serializer()` — instantiate the serializer
- `filter_backends` — attach filter classes
- `pagination_class` — attach pagination

```python
from rest_framework.generics import GenericAPIView
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

### 3.3 All Concrete Generic Views

```python
from rest_framework import generics
from .models import Book
from .serializers import BookSerializer


# POST /books/
class BookCreateView(generics.CreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# GET /books/
class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# GET /books/<pk>/
class BookRetrieveView(generics.RetrieveAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# PUT /books/<pk>/ and PATCH /books/<pk>/
class BookUpdateView(generics.UpdateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# DELETE /books/<pk>/
class BookDestroyView(generics.DestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# GET /books/ + POST /books/
class BookListCreateView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# GET /books/<pk>/ + PUT/PATCH /books/<pk>/
class BookRetrieveUpdateView(generics.RetrieveUpdateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# GET /books/<pk>/ + DELETE /books/<pk>/
class BookRetrieveDestroyView(generics.RetrieveDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


# GET /books/<pk>/ + PUT/PATCH + DELETE
class BookRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**URLs for full CRUD (2 views only):**

```python
from django.urls import path
from .views import BookListCreateView, BookRetrieveUpdateDestroyView

urlpatterns = [
    path('books/', BookListCreateView.as_view(), name='book-list'),
    path('books/<int:pk>/', BookRetrieveUpdateDestroyView.as_view(), name='book-detail'),
]
```

---

### 3.4 Customizing Generic Views

**Filter by query param:**

```python
class BookListView(generics.ListAPIView):
    serializer_class = BookSerializer

    def get_queryset(self):
        queryset = Book.objects.all()
        author = self.request.query_params.get('author')
        genre = self.request.query_params.get('genre')
        if author:
            queryset = queryset.filter(author__icontains=author)
        if genre:
            queryset = queryset.filter(genre=genre)
        return queryset
```

**Set extra fields on create:**

```python
class BookCreateView(generics.CreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def perform_create(self, serializer):
        serializer.save(created_by=self.request.user)  # auto-set from request
```

**Different serializers for list vs detail:**

```python
class BookRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()

    def get_serializer_class(self):
        if self.request.method == 'GET':
            return BookDetailSerializer    # more fields for detail
        return BookSerializer              # fewer fields for write
```

---

## 4. Mixins

### 4.1 What are Mixins

Mixins are behavior classes that provide individual CRUD actions.
Generic views are just `GenericAPIView` + combinations of these mixins.

| Mixin | HTTP Methods | Action method |
|---|---|---|
| `CreateModelMixin` | POST | `create()` |
| `ListModelMixin` | GET (list) | `list()` |
| `RetrieveModelMixin` | GET (detail) | `retrieve()` |
| `UpdateModelMixin` | PUT, PATCH | `update()`, `partial_update()` |
| `DestroyModelMixin` | DELETE | `destroy()` |

**Why learn mixins?**
- Understand what generic views do under the hood
- Combine exactly the actions you need
- Override behavior at the mixin level

---

### 4.2 All Mixins with Examples

```python
from rest_framework import generics, mixins
from .models import Book
from .serializers import BookSerializer


# Build your own combination
class BookListCreateView(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    generics.GenericAPIView
):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)


class BookRetrieveUpdateDestroyView(
    mixins.RetrieveModelMixin,
    mixins.UpdateModelMixin,
    mixins.DestroyModelMixin,
    generics.GenericAPIView
):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def patch(self, request, *args, **kwargs):
        return self.partial_update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

**Note:** `ListCreateAPIView` is exactly equivalent to the first class above — it's just a pre-built shorthand.

---

## 5. ViewSets

### 5.1 What is a ViewSet

A ViewSet combines all CRUD actions into a single class.
A **Router** auto-generates all URLs.
No need to write separate URL patterns for list, create, detail, update, delete.

**Advantages:**
- Minimal code for full CRUD
- Router handles URL generation
- Easy to add custom actions with `@action`

**Disadvantages:**
- Less explicit than separate views
- Harder to customize individual actions (though possible)

---

### 5.2 ModelViewSet

The most powerful and common ViewSet. Provides all 6 CRUD actions.

```python
# views.py
from rest_framework.viewsets import ModelViewSet
from .models import Book
from .serializers import BookSerializer

class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

```python
# urls.py
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register('books', BookViewSet)   # prefix = 'books'

urlpatterns = router.urls
```

**Auto-generated URLs:**

| URL | Method | Action | Name |
|---|---|---|---|
| `/books/` | GET | list | `book-list` |
| `/books/` | POST | create | `book-list` |
| `/books/<pk>/` | GET | retrieve | `book-detail` |
| `/books/<pk>/` | PUT | update | `book-detail` |
| `/books/<pk>/` | PATCH | partial_update | `book-detail` |
| `/books/<pk>/` | DELETE | destroy | `book-detail` |

---

### 5.3 ReadOnlyModelViewSet

Only `list` and `retrieve` — safe, read-only access.

```python
from rest_framework.viewsets import ReadOnlyModelViewSet

class BookViewSet(ReadOnlyModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

Auto-generates:
```
GET /books/        → list
GET /books/<pk>/   → retrieve
```

---

### 5.4 ViewSet and GenericViewSet

**ViewSet** — completely manual, no built-in actions:

```python
from rest_framework.viewsets import ViewSet
from rest_framework.response import Response

class BookViewSet(ViewSet):
    def list(self, request):
        return Response({'message': 'all books'})

    def retrieve(self, request, pk=None):
        return Response({'message': f'book {pk}'})

    def create(self, request):
        return Response({'message': 'created'})
```

**GenericViewSet** — adds queryset/serializer support, no actions (add mixins yourself):

```python
from rest_framework.viewsets import GenericViewSet
from rest_framework.mixins import ListModelMixin, RetrieveModelMixin

class BookViewSet(ListModelMixin, RetrieveModelMixin, GenericViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

---

### 5.5 Custom Actions with @action

Add non-CRUD endpoints to a ViewSet using `@action` decorator.

```python
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.viewsets import ModelViewSet
from .models import Book
from .serializers import BookSerializer

class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    # Collection action — /books/featured/
    @action(detail=False, methods=['get'])
    def featured(self, request):
        featured_books = Book.objects.filter(is_featured=True)
        serializer = self.get_serializer(featured_books, many=True)
        return Response(serializer.data)

    # Instance action — /books/<pk>/mark-featured/
    @action(detail=True, methods=['post'])
    def mark_featured(self, request, pk=None):
        book = self.get_object()
        book.is_featured = True
        book.save()
        return Response({'status': 'Book marked as featured.'})

    # Action with custom URL name — /books/<pk>/toggle-status/
    @action(detail=True, methods=['patch'], url_path='toggle-status')
    def toggle_status(self, request, pk=None):
        book = self.get_object()
        book.is_active = not book.is_active
        book.save()
        return Response({'is_active': book.is_active})
```

**`@action` parameters:**
- `detail=True` — works on a single object (`/books/<pk>/action/`)
- `detail=False` — works on collection (`/books/action/`)
- `methods=['get', 'post']` — allowed HTTP methods
- `url_path='custom-path'` — override URL slug (default: method name)
- `permission_classes=[...]` — per-action permissions

---

### 5.6 ViewSet Comparison Table

| Class | Actions available | Use when |
|---|---|---|
| `ViewSet` | None — write everything | Complete custom logic |
| `GenericViewSet` | None — add mixins | Selective CRUD actions |
| `ModelViewSet` | All 6 CRUD actions | Standard model CRUD |
| `ReadOnlyModelViewSet` | list, retrieve | Read-only APIs |

---

## 6. Pagination

### 6.1 What is Pagination

Pagination splits large querysets into pages so you don't return thousands of records at once.

**Global setup in settings.py:**

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}
```

**To disable pagination for a view:**

```python
class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = None   # disable for this view
```

---

### 6.2 PageNumberPagination

Pages accessed by number: `?page=1`, `?page=2`.
Most common and intuitive for users.

```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class BookPagination(PageNumberPagination):
    page_size = 10                          # default items per page
    page_size_query_param = 'page_size'     # allow ?page_size=5
    max_page_size = 100                     # maximum allowed
    page_query_param = 'page'              # ?page=2
```

**Response format:**
```json
{
    "count": 150,
    "next": "http://localhost:8000/api/books/?page=2",
    "previous": null,
    "results": [
        {"id": 1, "title": "Book 1"},
        ...
    ]
}
```

---

### 6.3 LimitOffsetPagination

Client controls limit (how many) and offset (start position).
`?limit=10&offset=20` → return 10 items starting from the 21st.

```python
from rest_framework.pagination import LimitOffsetPagination

class BookPagination(LimitOffsetPagination):
    default_limit = 10
    max_limit = 100
```

**Response format:**
```json
{
    "count": 150,
    "next": "http://localhost:8000/api/books/?limit=10&offset=30",
    "previous": "http://localhost:8000/api/books/?limit=10&offset=10",
    "results": [...]
}
```

**When to use:** When frontend needs fine-grained control over data windows.

---

### 6.4 CursorPagination

Uses an opaque cursor for navigation. Most efficient for large datasets.
Cannot jump to arbitrary pages — only next/previous.
Best for: infinite scroll, social feeds, real-time data.

```python
from rest_framework.pagination import CursorPagination

class BookCursorPagination(CursorPagination):
    page_size = 10
    ordering = '-created_at'    # must specify ordering field
    cursor_query_param = 'cursor'
```

**Response format:**
```json
{
    "next": "http://localhost:8000/api/books/?cursor=cD0yMDIz...",
    "previous": null,
    "results": [...]
}
```

**Advantages:** Consistent results even when data is added/removed mid-pagination.

---

### 6.5 Using Pagination in Views

**Attach to a Generic View:**
```python
from .pagination import BookPagination

class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = BookPagination
```

**Attach to a ViewSet:**
```python
class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = BookPagination
```

**Manual pagination in APIView:**
```python
class BookListView(APIView):
    def get(self, request):
        books = Book.objects.all()
        paginator = BookPagination()
        result_page = paginator.paginate_queryset(books, request)
        serializer = BookSerializer(result_page, many=True)
        return paginator.get_paginated_response(serializer.data)
```

---

## 7. Writing Best APIs — Industry Level

### 7.1 Standard API Response Format

Always wrap responses in a consistent envelope so frontend/mobile clients can handle them uniformly.

**Success response structure:**
```json
{
    "success": true,
    "message": "Book created successfully",
    "data": {
        "id": 1,
        "title": "Django for Beginners",
        "price": "29.99"
    }
}
```

**Error response structure:**
```json
{
    "success": false,
    "message": "Validation failed",
    "errors": {
        "title": ["This field is required."],
        "price": ["Price must be positive."]
    }
}
```

**Utility functions:**

```python
# utils/response.py
from rest_framework.response import Response
from rest_framework import status


def success_response(data=None, message="Success", status_code=status.HTTP_200_OK):
    return Response({
        "success": True,
        "message": message,
        "data": data
    }, status=status_code)


def created_response(data=None, message="Created successfully"):
    return Response({
        "success": True,
        "message": message,
        "data": data
    }, status=status.HTTP_201_CREATED)


def error_response(message="An error occurred", errors=None, status_code=status.HTTP_400_BAD_REQUEST):
    return Response({
        "success": False,
        "message": message,
        "errors": errors or {}
    }, status=status_code)


def not_found_response(message="Resource not found"):
    return Response({
        "success": False,
        "message": message,
        "errors": {}
    }, status=status.HTTP_404_NOT_FOUND)
```

**Usage in views:**

```python
from utils.response import success_response, created_response, error_response

class BookListView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer(queryset, many=True)
        return success_response(data=serializer.data, message="Books fetched successfully")

    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return created_response(data=serializer.data, message="Book created successfully")
```

---

### 7.2 Custom Exception Handler

Make ALL error responses follow your standard format automatically.

```python
# utils/exceptions.py
from rest_framework.views import exception_handler
from rest_framework.exceptions import ValidationError, NotFound, PermissionDenied, AuthenticationFailed


def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)

    if response is not None:
        error_data = {
            "success": False,
            "message": "An error occurred",
            "errors": {}
        }

        if isinstance(exc, ValidationError):
            error_data["message"] = "Validation failed"
            error_data["errors"] = response.data
        elif isinstance(exc, NotFound):
            error_data["message"] = "Resource not found"
        elif isinstance(exc, PermissionDenied):
            error_data["message"] = "You do not have permission to perform this action"
        elif isinstance(exc, AuthenticationFailed):
            error_data["message"] = "Authentication failed"
        else:
            error_data["message"] = str(exc)

        response.data = error_data

    return response
```

```python
# settings.py
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'utils.exceptions.custom_exception_handler'
}
```

---

### 7.3 URL Design — REST Naming Conventions

**Core rules:**
- Use **plural nouns**: `/books/` not `/book/`
- Use **HTTP methods** for actions, not verbs in URLs
- Use **lowercase**: `/book-categories/` not `/bookCategories/`
- Use **hyphens**, not underscores: `/book-reviews/` not `/book_reviews/`
- Nest max **2 levels** deep

**Standard patterns:**

```
# Resource collection
GET    /api/books/              → list all books
POST   /api/books/              → create a book

# Single resource
GET    /api/books/5/            → get book with id 5
PUT    /api/books/5/            → full update book 5
PATCH  /api/books/5/            → partial update book 5
DELETE /api/books/5/            → delete book 5

# Nested resources (relationships)
GET    /api/books/5/reviews/    → all reviews for book 5
POST   /api/books/5/reviews/    → add a review to book 5
GET    /api/books/5/reviews/2/  → review 2 of book 5

# Custom collection actions
GET    /api/books/featured/     → featured books
GET    /api/books/search/       → search books

# Custom instance actions
POST   /api/books/5/publish/    → publish book 5
POST   /api/books/5/archive/    → archive book 5
```

**Bad URL patterns to avoid:**

```
# WRONG — verbs in URL
GET  /api/getBooks/
POST /api/createBook/
GET  /api/getBookById/5/

# WRONG — singular noun
GET  /api/book/
GET  /api/book/5/

# WRONG — mixed case
GET  /api/bookCategories/

# WRONG — too deeply nested
GET  /api/books/5/reviews/2/comments/7/likes/
```

---

### 7.4 Exposing Choices to Frontend

```python
# models.py
class Book(models.Model):
    GENRE_CHOICES = [
        ('fiction', 'Fiction'),
        ('non_fiction', 'Non Fiction'),
        ('science', 'Science'),
        ('biography', 'Biography'),
        ('history', 'History'),
    ]

    STATUS_CHOICES = [
        ('draft', 'Draft'),
        ('published', 'Published'),
        ('archived', 'Archived'),
    ]

    genre = models.CharField(max_length=20, choices=GENRE_CHOICES, default='fiction')
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='draft')
```

```python
# views.py — expose all choices at one endpoint
class BookChoicesView(APIView):
    permission_classes = [AllowAny]

    def get(self, request):
        return Response({
            'genre_choices': [
                {'value': k, 'label': v}
                for k, v in Book.GENRE_CHOICES
            ],
            'status_choices': [
                {'value': k, 'label': v}
                for k, v in Book.STATUS_CHOICES
            ]
        })
```

**Frontend receives:**
```json
{
    "genre_choices": [
        {"value": "fiction", "label": "Fiction"},
        {"value": "non_fiction", "label": "Non Fiction"},
        {"value": "science", "label": "Science"}
    ],
    "status_choices": [
        {"value": "draft", "label": "Draft"},
        {"value": "published", "label": "Published"}
    ]
}
```

---

### 7.5 API Documentation with Swagger

**Install drf-spectacular (recommended):**

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
    'TITLE': 'Book Store API',
    'DESCRIPTION': 'API documentation for the Book Store project',
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

Visit `http://localhost:8000/api/docs/` for interactive Swagger UI.

**Add descriptions to views:**

```python
from drf_spectacular.utils import extend_schema, OpenApiParameter

class BookListCreateView(generics.ListCreateAPIView):
    """
    get: Returns paginated list of all books.
    post: Creates a new book. Requires authentication.
    """
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    @extend_schema(
        parameters=[
            OpenApiParameter('author', str, description='Filter by author name'),
            OpenApiParameter('genre', str, description='Filter by genre'),
        ]
    )
    def get(self, request, *args, **kwargs):
        return super().get(request, *args, **kwargs)
```

---

### 7.6 Linked and Nested APIs

**Nested object (read):**

```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['id', 'name', 'bio']


class BookSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)       # nested object on read
    author_id = serializers.IntegerField(write_only=True)  # accept ID on write

    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'author_id', 'price']
```

**Response:**
```json
{
    "id": 1,
    "title": "Django Book",
    "author": {
        "id": 5,
        "name": "John Doe",
        "bio": "Django expert"
    },
    "price": "29.99"
}
```

**Nested ViewSet URLs:**

```python
# urls.py — manual nested URLs
urlpatterns = [
    path('books/', BookListCreateView.as_view()),
    path('books/<int:pk>/', BookRetrieveUpdateDestroyView.as_view()),
    path('books/<int:book_pk>/reviews/', ReviewListCreateView.as_view()),
    path('books/<int:book_pk>/reviews/<int:pk>/', ReviewDetailView.as_view()),
]
```

```python
# views.py — nested review view
class ReviewListCreateView(generics.ListCreateAPIView):
    serializer_class = ReviewSerializer

    def get_queryset(self):
        return Review.objects.filter(book_id=self.kwargs['book_pk'])

    def perform_create(self, serializer):
        book = get_object_or_404(Book, pk=self.kwargs['book_pk'])
        serializer.save(book=book, author=self.request.user)
```

---

### 7.7 Error Response Standards

**Standard error codes your API should return:**

| Status | When |
|---|---|
| `400 Bad Request` | Validation failed, malformed request |
| `401 Unauthorized` | Not authenticated (no token / session) |
| `403 Forbidden` | Authenticated but no permission |
| `404 Not Found` | Resource does not exist |
| `405 Method Not Allowed` | HTTP method not supported |
| `409 Conflict` | Duplicate resource (e.g., email exists) |
| `422 Unprocessable Entity` | Semantically invalid data |
| `429 Too Many Requests` | Rate limit exceeded |
| `500 Internal Server Error` | Unhandled server error |

**Custom 404 and 500 handlers (Django-level):**

```python
# urls.py (project level)
handler404 = 'utils.views.custom_404'
handler500 = 'utils.views.custom_500'

# utils/views.py
from django.http import JsonResponse

def custom_404(request, exception):
    return JsonResponse({
        "success": False,
        "message": "The resource you are looking for does not exist.",
        "errors": {}
    }, status=404)

def custom_500(request):
    return JsonResponse({
        "success": False,
        "message": "Internal server error. Please try again later.",
        "errors": {}
    }, status=500)
```

---

## Quick Reference

### DRF Required Settings

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
    'EXCEPTION_HANDLER': 'utils.exceptions.custom_exception_handler',
}
```

### Project Structure

```
myproject/
├── manage.py
├── myproject/
│   ├── settings.py
│   └── urls.py
├── books/
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   ├── urls.py
│   └── pagination.py
└── utils/
    ├── response.py
    ├── exceptions.py
    └── views.py
```

### When to Use What

| Scenario | Use |
|---|---|
| Full CRUD, standard model | `ModelViewSet` + Router |
| List + Create only | `ListCreateAPIView` |
| Read-only API | `ReadOnlyModelViewSet` |
| Complex custom logic | `APIView` |
| Standard CRUD endpoints | Generic Views |
| Selective actions | `GenericViewSet` + Mixins |
| Non-model data | `Serializer` or `BaseSerializer` |
| Model-backed data | `ModelSerializer` |
| Large datasets pagination | `CursorPagination` |
| Simple page navigation | `PageNumberPagination` |
| Frontend-controlled window | `LimitOffsetPagination` |
