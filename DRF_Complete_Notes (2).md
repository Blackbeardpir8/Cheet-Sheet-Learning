# Django REST Framework — Complete Notes

---

## Table of Contents

1. [DRF Basics](#1-drf-basics)
   - 1.1 [Request & Response Objects](#11-request--response-objects)
   - 1.2 [Status Codes](#12-status-codes)
   - 1.3 [APIView (Deep Dive)](#13-apiview-deep-dive)

2. [DRF Serializers (Advanced)](#2-drf-serializers-advanced)
   - 2.1 [Nested Serializers](#21-nested-serializers)
   - 2.2 [Writable Nested Serializers](#22-writable-nested-serializers)
   - 2.3 [Serializer Validation](#23-serializer-validation)
   - 2.4 [Serializer Context](#24-serializer-context)
   - 2.5 [Dynamic Fields](#25-dynamic-fields)

3. [DRF Views (Important)](#3-drf-views-important)
   - 3.1 [APIView (Deep)](#31-apiview-deep)
   - 3.2 [GenericAPIView](#32-genericapiview)
   - 3.3 [Mixins](#33-mixins)
   - 3.4 [ViewSets](#34-viewsets)
   - 3.5 [ModelViewSet](#35-modelviewset)
   - 3.6 [Router System](#36-router-system)

4. [DRF Permissions](#4-drf-permissions)
   - 4.1 [IsAuthenticated](#41-isauthenticated)
   - 4.2 [IsAdminUser](#42-isadminuser)
   - 4.3 [Custom Permissions](#43-custom-permissions)

5. [DRF Authentication](#5-drf-authentication)
   - 5.1 [Token Authentication](#51-token-authentication)
   - 5.2 [JWT Authentication (Very Important)](#52-jwt-authentication-very-important)
   - 5.3 [Session Authentication](#53-session-authentication)

6. [DRF Throttling](#6-drf-throttling)
   - 6.1 [User Rate Limiting](#61-user-rate-limiting)
   - 6.2 [Scoped Throttling](#62-scoped-throttling)

7. [DRF Filtering & Search](#7-drf-filtering--search)
   - 7.1 [DjangoFilterBackend](#71-djangofilterbackend)
   - 7.2 [SearchFilter](#72-searchfilter)
   - 7.3 [OrderingFilter](#73-orderingfilter)

8. [DRF Pagination (Advanced)](#8-drf-pagination-advanced)
   - 8.1 [PageNumberPagination](#81-pagenumberpagination)
   - 8.2 [LimitOffsetPagination](#82-limitoffsetpagination)
   - 8.3 [CursorPagination](#83-cursorpagination)

9. [Real Project Architecture](#9-real-project-architecture)
   - 9.1 [Service Layer Pattern](#91-service-layer-pattern)
   - 9.2 [Repository Pattern](#92-repository-pattern)
   - 9.3 [Clean Architecture Basics](#93-clean-architecture-basics)
   - 9.4 [Folder Structure for Large Apps](#94-folder-structure-for-large-apps)
   - 9.5 [Environment-Based Settings](#95-environment-based-settings)

10. [Advanced Authentication & Security](#10-advanced-authentication--security)
    - 10.1 [JWT Access + Refresh Tokens](#101-jwt-access--refresh-tokens)
    - 10.2 [OAuth Basics (Google Login)](#102-oauth-basics-google-login)
    - 10.3 [Role-Based Access Control (RBAC)](#103-role-based-access-control-rbac)
    - 10.4 [API Security Best Practices](#104-api-security-best-practices)
    - 10.5 [Rate Limiting (DDoS Protection)](#105-rate-limiting-ddos-protection)

11. [Performance Optimization](#11-performance-optimization)
    - 11.1 [Database Indexing](#111-database-indexing)
    - 11.2 [Query Optimization Deep Dive](#112-query-optimization-deep-dive)
    - 11.3 [N+1 Problem (Very Important)](#113-n1-problem-very-important)
    - 11.4 [select_related vs prefetch_related](#114-select_related-vs-prefetch_related)
    - 11.5 [Redis Caching (Real-World)](#115-redis-caching-real-world)

12. [Testing](#12-testing)
    - 12.1 [Django TestCase](#121-django-testcase)
    - 12.2 [API Testing with DRF](#122-api-testing-with-drf)
    - 12.3 [pytest](#123-pytest)
    - 12.4 [Factory Boy](#124-factory-boy)
    - 12.5 [Postman / API Testing Collections](#125-postman--api-testing-collections)

13. [Background Tasks](#13-background-tasks)
    - 13.1 [Celery (Deep)](#131-celery-deep)
    - 13.2 [Redis / RabbitMQ as Broker](#132-redis--rabbitmq-as-broker)
    - 13.3 [Async Tasks (Emails, Payments)](#133-async-tasks-emails-payments)

14. [API Documentation](#14-api-documentation)
    - 14.1 [Swagger / OpenAPI](#141-swagger--openapi)
    - 14.2 [drf-spectacular](#142-drf-spectacular)
    - 14.3 [Auto API Docs Setup](#143-auto-api-docs-setup)

---

# 1. DRF Basics

## 1.1 Request & Response Objects

### Simple Definition
DRF gives you its own `Request` and `Response` objects on top of Django's default ones. They are smarter and easier to use for APIs.

### Why It Is Used
- Django's default `HttpRequest` doesn't handle JSON data well.
- DRF's `Request` can parse JSON, form data, and more automatically.
- DRF's `Response` automatically formats output (JSON, XML, etc.) based on what the client wants.

### Types
- **`Request`** — wraps Django's `HttpRequest`, adds `request.data` (works for POST, PUT, PATCH).
- **`Response`** — wraps Django's `HttpResponse`, handles content negotiation.

### Advantages
- `request.data` works for all HTTP methods (not just POST).
- `Response` auto-negotiates content type (JSON, HTML, etc.).
- Cleaner and simpler code.

### Disadvantages
- Slight overhead compared to raw Django views.
- Need to import DRF — extra dependency.

### Tips & Tricks
- Always use `request.data` instead of `request.POST` in DRF views.
- `request.query_params` is the DRF way to access URL query parameters (`?search=abc`).
- `Response(data, status=status.HTTP_200_OK)` is the standard way to return data.

### Best Practices
- Always pass a `status` code in `Response`.
- Use `status` module (`from rest_framework import status`) for readable status codes.

### Example Code

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class HelloView(APIView):
    def get(self, request):
        name = request.query_params.get('name', 'World')
        return Response({"message": f"Hello, {name}!"}, status=status.HTTP_200_OK)

    def post(self, request):
        data = request.data  # Handles JSON automatically
        return Response({"you_sent": data}, status=status.HTTP_201_CREATED)
```

---

## 1.2 Status Codes

### Simple Definition
Status codes are 3-digit numbers that tell the client what happened with their request (success, error, not found, etc.).

### Why It Is Used
Without status codes, the client doesn't know if their request worked or failed. It's the standard way APIs communicate results.

### Types (Most Common)

| Code | Meaning | When to Use |
|------|---------|-------------|
| 200 | OK | Successful GET, PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Validation error |
| 401 | Unauthorized | Not logged in |
| 403 | Forbidden | No permission |
| 404 | Not Found | Resource missing |
| 500 | Server Error | Bug in your code |

### Best Practices
- Always use `status` module constants (e.g., `status.HTTP_200_OK`) — never hardcode numbers.
- Return `201` for creation, not `200`.
- Return `204` for delete with no response body.

### Example Code

```python
from rest_framework import status
from rest_framework.response import Response

# Good
return Response(data, status=status.HTTP_201_CREATED)

# Bad — don't hardcode
return Response(data, status=201)
```

---

## 1.3 APIView (Deep Dive)

### Simple Definition
`APIView` is the most basic class-based view in DRF. You define methods like `get()`, `post()`, `put()`, `delete()` to handle HTTP requests manually.

### Why It Is Used
- Full control over request handling.
- Good for custom logic that doesn't fit standard CRUD.
- Foundation for all other DRF views.

### Advantages
- Complete control over every request.
- Easy to understand — each HTTP method is a separate function.
- Can apply permissions, authentication, throttling at view level.

### Disadvantages
- More boilerplate compared to Generic Views.
- No automatic serializer/queryset integration.

### Tips & Tricks
- Always call `serializer.is_valid(raise_exception=True)` to auto-return 400 on errors.
- Use `get_object_or_404` from Django to handle missing objects.
- Class-level settings override global settings.

### Best Practices
- Use `APIView` for non-standard APIs (login, dashboard, analytics).
- For standard CRUD, prefer `GenericAPIView` or `ModelViewSet`.

### Step-by-Step Setup

**Step 1: Install DRF**
```bash
pip install djangorestframework
```

**Step 2: Add to settings**
```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

**Step 3: Create model**
```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=6, decimal_places=2)

    def __str__(self):
        return self.title
```

**Step 4: Create serializer**
```python
# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

**Step 5: Create view**
```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404
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
    def get(self, request, pk):
        book = get_object_or_404(Book, pk=pk)
        serializer = BookSerializer(book)
        return Response(serializer.data)

    def put(self, request, pk):
        book = get_object_or_404(Book, pk=pk)
        serializer = BookSerializer(book, data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)

    def delete(self, request, pk):
        book = get_object_or_404(Book, pk=pk)
        book.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

**Step 6: URL setup**
```python
# urls.py
from django.urls import path
from .views import BookListView, BookDetailView

urlpatterns = [
    path('books/', BookListView.as_view()),
    path('books/<int:pk>/', BookDetailView.as_view()),
]
```

---

# 2. DRF Serializers (Advanced)

## 2.1 Nested Serializers

### Simple Definition
A nested serializer means one serializer is placed inside another. Used when a model has a ForeignKey or related field and you want full object data (not just an ID) in the response.

### Why It Is Used
Instead of returning just `author_id: 3`, you return the full author object `{id: 3, name: "Alice"}`. Much more useful for frontend.

### Example

```python
# models.py
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

```python
# serializers.py
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['id', 'name', 'email']

class BookSerializer(serializers.ModelSerializer):
    author = AuthorSerializer()  # <-- Nested!

    class Meta:
        model = Book
        fields = ['id', 'title', 'author']
```

**Response output:**
```json
{
  "id": 1,
  "title": "Django Book",
  "author": {
    "id": 3,
    "name": "Alice",
    "email": "alice@example.com"
  }
}
```

### Tips
- Nested serializers are **read-only by default**.
- For write operations, you need to override `create()` and `update()`.

---

## 2.2 Writable Nested Serializers

### Simple Definition
Writable nested serializers let you create or update related objects in a single API call.

### Why It Is Used
Instead of two API calls (create author, then create book), you can do it in one request.

### Example

```python
class BookSerializer(serializers.ModelSerializer):
    author = AuthorSerializer()

    class Meta:
        model = Book
        fields = ['id', 'title', 'author']

    def create(self, validated_data):
        author_data = validated_data.pop('author')
        author = Author.objects.create(**author_data)
        book = Book.objects.create(author=author, **validated_data)
        return book

    def update(self, instance, validated_data):
        author_data = validated_data.pop('author', None)
        if author_data:
            for key, value in author_data.items():
                setattr(instance.author, key, value)
            instance.author.save()
        for key, value in validated_data.items():
            setattr(instance, key, value)
        instance.save()
        return instance
```

---

## 2.3 Serializer Validation

### Simple Definition
Validation checks that the data sent by the user is correct before saving it to the database.

### Types

**Field-level validation** — validates a single field:
```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'

    def validate_price(self, value):
        if value < 0:
            raise serializers.ValidationError("Price cannot be negative.")
        return value
```

**Object-level validation** — validates multiple fields together:
```python
    def validate(self, data):
        if data['start_date'] > data['end_date']:
            raise serializers.ValidationError("Start date must be before end date.")
        return data
```

### Tips
- Field-level: use `validate_<fieldname>`.
- Object-level: use `validate(self, data)`.
- Always raise `serializers.ValidationError` — never return `None`.

---

## 2.4 Serializer Context

### Simple Definition
Context lets you pass extra information (like the request object) into a serializer from a view.

### Why It Is Used
Sometimes the serializer needs access to the current user, the request method, or other view-level data to make decisions.

### Example

```python
# In view — pass context
class BookListView(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True, context={'request': request})
        return Response(serializer.data)

# In serializer — use context
class BookSerializer(serializers.ModelSerializer):
    is_owner = serializers.SerializerMethodField()

    class Meta:
        model = Book
        fields = ['id', 'title', 'is_owner']

    def get_is_owner(self, obj):
        request = self.context.get('request')
        return obj.author == request.user
```

### Tips
- `GenericAPIView` and `ModelViewSet` automatically pass `request` in context.
- Access context via `self.context['request']`.

---

## 2.5 Dynamic Fields

### Simple Definition
Dynamic fields let the API caller choose which fields to include in the response via a query parameter like `?fields=id,title`.

### Why It Is Used
Reduces unnecessary data transfer — the client only gets what it needs.

### Example

```python
class DynamicFieldsSerializer(serializers.ModelSerializer):
    def __init__(self, *args, **kwargs):
        fields = kwargs.pop('fields', None)
        super().__init__(*args, **kwargs)
        if fields:
            allowed = set(fields)
            existing = set(self.fields.keys())
            for field_name in existing - allowed:
                self.fields.pop(field_name)

class BookSerializer(DynamicFieldsSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price']

# In view:
class BookListView(APIView):
    def get(self, request):
        fields_param = request.query_params.get('fields')
        fields = fields_param.split(',') if fields_param else None
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True, fields=fields)
        return Response(serializer.data)

# API call: GET /books/?fields=id,title
```

---

# 3. DRF Views (Important)

## 3.1 APIView (Deep)

> See [Section 1.3](#13-apiview-deep-dive) for full setup and code.

### Key Points
- Manually handle each HTTP method.
- Class-level settings: `authentication_classes`, `permission_classes`, `throttle_classes`.
- Most flexible but most verbose.

```python
class MyView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated]
    throttle_classes = [UserRateThrottle]

    def get(self, request):
        return Response({"status": "ok"})
```

---

## 3.2 GenericAPIView

### Simple Definition
`GenericAPIView` extends `APIView` with built-in support for querysets, serializers, and pagination. It's the base for all generic views.

### Why It Is Used
Reduces boilerplate — you define `queryset` and `serializer_class` once and the view handles the rest.

### Example

```python
from rest_framework.generics import GenericAPIView
from rest_framework.mixins import ListModelMixin

class BookListView(ListModelMixin, GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request):
        return self.list(request)
```

### Key Attributes
- `queryset` — the data to work with.
- `serializer_class` — how to serialize/deserialize data.
- `lookup_field` — field used to find single objects (default: `pk`).
- `pagination_class` — pagination to use.

---

## 3.3 Mixins

### Simple Definition
Mixins are small reusable classes that add specific behavior (list, create, retrieve, update, destroy) to a view. They work together with `GenericAPIView`.

### Types

| Mixin | Method it Adds | HTTP Action |
|-------|---------------|-------------|
| `ListModelMixin` | `list()` | GET (all) |
| `CreateModelMixin` | `create()` | POST |
| `RetrieveModelMixin` | `retrieve()` | GET (one) |
| `UpdateModelMixin` | `update()` | PUT/PATCH |
| `DestroyModelMixin` | `destroy()` | DELETE |

### Example

```python
from rest_framework import mixins, generics

class BookListCreateView(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    generics.GenericAPIView
):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request):
        return self.list(request)

    def post(self, request):
        return self.create(request)
```

### Shortcut Generic Views (DRF provides these ready-made)

```python
from rest_framework import generics

# GET list + POST create
class BookListCreateView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

# GET single + PUT/PATCH update + DELETE
class BookDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

---

## 3.4 ViewSets

### Simple Definition
A `ViewSet` combines multiple views (list, create, retrieve, update, destroy) into a single class. Instead of defining separate `get`, `post`, etc., you define `list`, `create`, `retrieve`, `update`, `destroy`.

### Why It Is Used
Reduces the number of classes. One ViewSet = all CRUD operations.

### Example

```python
from rest_framework import viewsets
from rest_framework.response import Response

class BookViewSet(viewsets.ViewSet):
    def list(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk=None):
        book = get_object_or_404(Book, pk=pk)
        serializer = BookSerializer(book)
        return Response(serializer.data)
```

---

## 3.5 ModelViewSet

### Simple Definition
`ModelViewSet` is the most powerful view in DRF. It automatically provides `list`, `create`, `retrieve`, `update`, `partial_update`, and `destroy` actions — all in one class with just a queryset and serializer.

### Example

```python
from rest_framework import viewsets

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

That's it! This creates all 6 endpoints automatically.

### Custom Actions with `@action`

```python
from rest_framework.decorators import action

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    @action(detail=False, methods=['get'])
    def cheap_books(self, request):
        books = Book.objects.filter(price__lt=200)
        serializer = self.get_serializer(books, many=True)
        return Response(serializer.data)
    # URL: GET /books/cheap_books/
```

---

## 3.6 Router System

### Simple Definition
Routers automatically generate URLs for ViewSets. Instead of writing URLs manually, one router setup covers all endpoints.

### Types
- `SimpleRouter` — basic URL patterns.
- `DefaultRouter` — same + adds an API root view.

### Example

```python
# urls.py
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register('books', BookViewSet, basename='book')

urlpatterns = router.urls
```

**Auto-generated URLs:**

| URL | Method | Action |
|-----|--------|--------|
| `/books/` | GET | list |
| `/books/` | POST | create |
| `/books/{pk}/` | GET | retrieve |
| `/books/{pk}/` | PUT | update |
| `/books/{pk}/` | PATCH | partial_update |
| `/books/{pk}/` | DELETE | destroy |

---

# 4. DRF Permissions

## 4.1 IsAuthenticated

### Simple Definition
`IsAuthenticated` allows only logged-in (authenticated) users to access the view.

### Setup

```python
# settings.py — global default
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
```

```python
# views.py — per view
from rest_framework.permissions import IsAuthenticated

class BookListView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({"message": "You are logged in!"})
```

---

## 4.2 IsAdminUser

### Simple Definition
Only allows users where `user.is_staff == True`.

```python
from rest_framework.permissions import IsAdminUser

class AdminView(APIView):
    permission_classes = [IsAdminUser]

    def get(self, request):
        return Response({"message": "Admin only"})
```

---

## 4.3 Custom Permissions

### Simple Definition
Custom permissions let you write your own rules — for example, "only the owner of an object can edit it".

### Example

```python
# permissions.py
from rest_framework.permissions import BasePermission

class IsOwnerOrReadOnly(BasePermission):
    """
    Allow anyone to read, but only the owner to write.
    """
    def has_object_permission(self, request, view, obj):
        if request.method in ('GET', 'HEAD', 'OPTIONS'):
            return True  # read is allowed for everyone
        return obj.owner == request.user  # write only for owner
```

```python
# views.py
from .permissions import IsOwnerOrReadOnly

class BookDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsAuthenticated, IsOwnerOrReadOnly]
```

### Tips
- `has_permission(request, view)` — called before the view runs (good for list views).
- `has_object_permission(request, view, obj)` — called on specific objects (good for detail views).

---

# 5. DRF Authentication

## 5.1 Token Authentication

### Simple Definition
Token Auth gives every user a unique token (a long random string). The client sends this token in the request header, and the server verifies it.

### Setup

```python
# settings.py
INSTALLED_APPS = ['rest_framework.authtoken']

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ]
}
```

```bash
python manage.py migrate
```

```python
# views.py — login endpoint
from rest_framework.authtoken.models import Token
from rest_framework.views import APIView
from rest_framework.response import Response
from django.contrib.auth import authenticate

class LoginView(APIView):
    def post(self, request):
        username = request.data.get('username')
        password = request.data.get('password')
        user = authenticate(username=username, password=password)
        if user:
            token, _ = Token.objects.get_or_create(user=user)
            return Response({'token': token.key})
        return Response({'error': 'Invalid credentials'}, status=400)
```

**Client sends:** `Authorization: Token abc123xyz456`

---

## 5.2 JWT Authentication (Very Important)

### Simple Definition
JWT (JSON Web Token) is a modern, stateless way to authenticate. Unlike Token Auth, JWTs contain user data inside the token itself — no database lookup needed.

### Why It Is Used
- Faster (no DB hit on every request).
- Works well for mobile apps, SPAs, microservices.
- Supports access + refresh token pattern.

### JWT Structure
`header.payload.signature` — three parts separated by dots.

### Setup

```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ]
}

from datetime import timedelta
SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=15),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,
}
```

```python
# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view()),       # Login → get tokens
    path('api/token/refresh/', TokenRefreshView.as_view()),  # Refresh access token
]
```

**Login request:**
```json
POST /api/token/
{"username": "alice", "password": "secret"}
```

**Response:**
```json
{
  "access": "eyJ0...",   // Short-lived (15 min)
  "refresh": "eyJ1..."   // Long-lived (7 days)
}
```

**Client sends access token:** `Authorization: Bearer eyJ0...`

**Refresh flow:** When access token expires, send refresh token to `/api/token/refresh/` to get a new access token.

---

## 5.3 Session Authentication

### Simple Definition
Session Auth uses Django's built-in session system (cookies). After login, the server gives the browser a session cookie.

### When to Use
- Django templates + DRF (hybrid projects).
- Browsable API during development.
- Not recommended for mobile apps or pure APIs.

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
    ]
}
```

---

# 6. DRF Throttling

## 6.1 User Rate Limiting

### Simple Definition
Throttling limits how many requests a user can make in a time period (e.g., 100 requests per hour).

### Why It Is Used
Prevents abuse, spam, and DDoS attacks.

### Setup

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.UserRateThrottle',
        'rest_framework.throttling.AnonRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'user': '100/hour',   # Logged-in users
        'anon': '20/hour',    # Anonymous users
    }
}
```

---

## 6.2 Scoped Throttling

### Simple Definition
Scoped throttling applies different limits to different views/endpoints.

### Example

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.ScopedRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'login': '5/hour',
        'search': '30/minute',
    }
}
```

```python
# views.py
class LoginView(APIView):
    throttle_scope = 'login'  # Only 5 login attempts per hour

class SearchView(APIView):
    throttle_scope = 'search'
```

---

# 7. DRF Filtering & Search

## 7.1 DjangoFilterBackend

### Simple Definition
Allows filtering queryset by exact field values via URL query parameters.

```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS = ['django_filters']

REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend']
}
```

```python
# views.py
from django_filters.rest_framework import DjangoFilterBackend

class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ['author', 'price']  # ?author=1&price=299

# Custom FilterSet
import django_filters

class BookFilter(django_filters.FilterSet):
    min_price = django_filters.NumberFilter(field_name='price', lookup_expr='gte')
    max_price = django_filters.NumberFilter(field_name='price', lookup_expr='lte')

    class Meta:
        model = Book
        fields = ['author', 'min_price', 'max_price']

class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filterset_class = BookFilter
    # URL: /books/?min_price=100&max_price=500
```

---

## 7.2 SearchFilter

### Simple Definition
Full-text search across one or more text fields.

```python
from rest_framework.filters import SearchFilter

class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [SearchFilter]
    search_fields = ['title', 'author__name']  # ?search=django
    # ^ prefix = exact, $ = regex, @ = full-text
```

---

## 7.3 OrderingFilter

### Simple Definition
Lets the client sort results by specified fields.

```python
from rest_framework.filters import OrderingFilter

class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [OrderingFilter]
    ordering_fields = ['price', 'title']
    ordering = ['title']  # Default ordering
    # URL: /books/?ordering=price  or  ?ordering=-price (descending)
```

---

# 8. DRF Pagination (Advanced)

## 8.1 PageNumberPagination

### Simple Definition
Splits results into pages: page 1, page 2, page 3...

### Setup

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}
```

**Custom Pagination:**
```python
# pagination.py
from rest_framework.pagination import PageNumberPagination

class StandardPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'  # ?page_size=20
    max_page_size = 100

# Usage: /books/?page=2&page_size=5
```

**Response format:**
```json
{
  "count": 100,
  "next": "http://api.example.com/books/?page=3",
  "previous": "http://api.example.com/books/?page=1",
  "results": [...]
}
```

---

## 8.2 LimitOffsetPagination

### Simple Definition
Client specifies `limit` (how many items) and `offset` (where to start).

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 10
}
# URL: /books/?limit=5&offset=10
```

---

## 8.3 CursorPagination

### Simple Definition
Uses a cursor (encoded position pointer) instead of page numbers. Fastest for large datasets — no page skipping.

```python
from rest_framework.pagination import CursorPagination

class BookCursorPagination(CursorPagination):
    page_size = 10
    ordering = 'created_at'  # Must be an ordered field

class BookListView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = BookCursorPagination
```

### When to Use What

| Pagination | Best For |
|-----------|---------|
| PageNumber | Simple apps, admin panels |
| LimitOffset | Flexible clients (mobile, SPA) |
| Cursor | Large data, real-time feeds, infinite scroll |

---

# 9. Real Project Architecture

## 9.1 Service Layer Pattern

### Simple Definition
The service layer is a middle layer between your views and models. All business logic lives here — views just call services, services do the work.

### Why It Is Used
- Views stay thin (easy to read).
- Business logic is testable and reusable.
- No logic duplication across views.

### Example Structure
```
books/
  views.py      ← thin, just calls service
  services.py   ← all business logic here
  models.py     ← database only
  serializers.py
```

```python
# services.py
from .models import Book

class BookService:
    @staticmethod
    def get_all_books():
        return Book.objects.all()

    @staticmethod
    def create_book(data):
        # Validate, check duplicates, send emails, etc.
        return Book.objects.create(**data)

    @staticmethod
    def get_cheap_books(max_price):
        return Book.objects.filter(price__lte=max_price)
```

```python
# views.py
from .services import BookService
from .serializers import BookSerializer
from rest_framework.views import APIView
from rest_framework.response import Response

class BookListView(APIView):
    def get(self, request):
        books = BookService.get_all_books()  # <-- thin view!
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = BookSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        book = BookService.create_book(serializer.validated_data)
        return Response(BookSerializer(book).data, status=201)
```

---

## 9.2 Repository Pattern

### Simple Definition
A repository is a class that wraps all database queries for a model. Instead of calling `Book.objects.filter(...)` everywhere, you call `BookRepository.get_cheap()`.

### Example

```python
# repositories.py
from .models import Book

class BookRepository:
    @staticmethod
    def get_all():
        return Book.objects.all()

    @staticmethod
    def get_by_id(pk):
        return Book.objects.get(pk=pk)

    @staticmethod
    def get_by_price_range(min_price, max_price):
        return Book.objects.filter(price__gte=min_price, price__lte=max_price)

    @staticmethod
    def create(data):
        return Book.objects.create(**data)
```

### Tips
- Repository handles **how** to get data.
- Service handles **what** to do with data.

---

## 9.3 Clean Architecture Basics

### Layers (Simplified)

```
Request
   ↓
Views (HTTP layer — receive/return HTTP)
   ↓
Serializers (data validation + transformation)
   ↓
Services (business logic)
   ↓
Repositories (data access)
   ↓
Models (database)
```

### Rules
- Views call serializers and services only.
- Services call repositories only.
- Repositories call models only.
- Never put business logic in models or views.

---

## 9.4 Folder Structure for Large Apps

```
project/
├── config/
│   ├── settings/
│   │   ├── base.py        ← common settings
│   │   ├── development.py ← dev settings
│   │   └── production.py  ← prod settings
│   ├── urls.py
│   └── wsgi.py
├── apps/
│   ├── users/
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── serializers.py
│   │   ├── services.py
│   │   ├── repositories.py
│   │   ├── permissions.py
│   │   ├── urls.py
│   │   └── tests/
│   └── books/
│       ├── models.py
│       ├── views.py
│       ├── serializers.py
│       ├── services.py
│       └── urls.py
├── core/
│   ├── permissions.py    ← shared custom permissions
│   ├── pagination.py     ← shared pagination
│   └── exceptions.py     ← custom exception handlers
├── .env
├── requirements.txt
└── manage.py
```

---

## 9.5 Environment-Based Settings

### Simple Definition
Separate settings files for development and production so you don't accidentally use dev configs in production.

### Setup

```python
# config/settings/base.py — shared settings
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', cast=bool, default=False)

INSTALLED_APPS = [
    'django.contrib.admin',
    'rest_framework',
    'apps.books',
    'apps.users',
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST', default='localhost'),
    }
}
```

```python
# config/settings/development.py
from .base import *

DEBUG = True
ALLOWED_HOSTS = ['*']
```

```python
# config/settings/production.py
from .base import *

DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com']
SECURE_HTTPS_REDIRECT = True
```

```bash
# .env file
SECRET_KEY=your-secret-key
DEBUG=True
DB_NAME=mydb
DB_USER=myuser
DB_PASSWORD=mypassword
```

```bash
pip install python-decouple
```

---

# 10. Advanced Authentication & Security

## 10.1 JWT Access + Refresh Tokens

### Simple Definition
- **Access Token** — short-lived (15 min), used for every API request.
- **Refresh Token** — long-lived (7 days), used ONLY to get a new access token.

### Flow
```
Login → get access + refresh tokens
Use access token for requests
Access token expires → use refresh token to get new access token
Refresh token expires → user must login again
```

### Custom JWT Payload (Add User Data to Token)

```python
# serializers.py
from rest_framework_simplejwt.serializers import TokenObtainPairSerializer

class MyTokenObtainPairSerializer(TokenObtainPairSerializer):
    @classmethod
    def get_token(cls, user):
        token = super().get_token(user)
        token['username'] = user.username
        token['email'] = user.email
        token['is_admin'] = user.is_staff
        return token

# views.py
from rest_framework_simplejwt.views import TokenObtainPairView

class MyTokenObtainPairView(TokenObtainPairView):
    serializer_class = MyTokenObtainPairSerializer
```

---

## 10.2 OAuth Basics (Google Login)

### Simple Definition
OAuth lets users log in with an external provider (Google, GitHub, Facebook) instead of creating a new password.

### Library

```bash
pip install social-auth-app-django
# or
pip install dj-rest-auth[with_social]
```

### Simplified Google OAuth Flow
1. Frontend redirects user to Google's login page.
2. Google redirects back with an `auth code`.
3. Backend exchanges `auth code` for user info.
4. Backend creates/finds the user and returns a JWT.

```python
# Google token verification (simplified)
import requests

class GoogleLoginView(APIView):
    def post(self, request):
        token = request.data.get('id_token')
        response = requests.get(
            f'https://oauth2.googleapis.com/tokeninfo?id_token={token}'
        )
        if response.status_code == 200:
            user_data = response.json()
            user, _ = User.objects.get_or_create(email=user_data['email'])
            jwt_token = get_tokens_for_user(user)
            return Response(jwt_token)
        return Response({'error': 'Invalid token'}, status=400)
```

---

## 10.3 Role-Based Access Control (RBAC)

### Simple Definition
RBAC assigns roles (admin, manager, viewer) to users and controls what each role can do.

### Implementation

```python
# models.py
class UserProfile(models.Model):
    ROLE_CHOICES = [('admin', 'Admin'), ('manager', 'Manager'), ('viewer', 'Viewer')]
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    role = models.CharField(max_length=20, choices=ROLE_CHOICES, default='viewer')
```

```python
# permissions.py
class IsManager(BasePermission):
    def has_permission(self, request, view):
        return (
            request.user.is_authenticated and
            hasattr(request.user, 'userprofile') and
            request.user.userprofile.role in ['admin', 'manager']
        )

class IsAdmin(BasePermission):
    def has_permission(self, request, view):
        return (
            request.user.is_authenticated and
            hasattr(request.user, 'userprofile') and
            request.user.userprofile.role == 'admin'
        )
```

---

## 10.4 API Security Best Practices

- Always use HTTPS in production.
- Store secrets in `.env` file — never hardcode.
- Set `DEBUG=False` in production.
- Use short-lived access tokens (15 min or less).
- Validate all input with serializers.
- Use `django-cors-headers` to restrict CORS origins.
- Rate limit all endpoints, especially auth endpoints.
- Never expose stack traces to API clients.

```python
# settings.py — production security headers
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_TYPE_NOSNIFF = True
X_FRAME_OPTIONS = 'DENY'
SECURE_HSTS_SECONDS = 31536000
```

---

## 10.5 Rate Limiting (DDoS Protection)

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '20/hour',
        'user': '1000/day',
    }
}
```

For serious DDoS protection, use a reverse proxy (Nginx, Cloudflare) in front of Django.

---

# 11. Performance Optimization

## 11.1 Database Indexing

### Simple Definition
An index is like a book's index — it lets the database find rows quickly without scanning every row.

### When to Add Index
- Fields used in `filter()`, `order_by()`, `get()`.
- Foreign keys (Django adds automatically).
- Fields you search by frequently.

```python
class Book(models.Model):
    title = models.CharField(max_length=200, db_index=True)  # Single index
    author = models.ForeignKey(Author, on_delete=models.CASCADE)  # Auto-indexed
    price = models.DecimalField(max_digits=6, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        indexes = [
            models.Index(fields=['price', 'created_at']),  # Composite index
        ]
```

---

## 11.2 Query Optimization Deep Dive

```python
# Bad — loads ALL fields
books = Book.objects.all()

# Good — only needed fields
books = Book.objects.only('id', 'title', 'price')

# Good — exclude heavy fields
books = Book.objects.defer('description')

# values() — returns dicts, faster than model instances
books = Book.objects.values('id', 'title')

# values_list() — returns tuples, even faster
prices = Book.objects.values_list('price', flat=True)

# count() — faster than len(queryset)
count = Book.objects.filter(price__lt=200).count()

# exists() — faster than bool(queryset)
if Book.objects.filter(title='Django').exists():
    ...
```

---

## 11.3 N+1 Problem (Very Important)

### Simple Definition
The N+1 problem happens when you run 1 query to get a list, then N more queries to get related data for each item in the list.

### Example of the Problem

```python
books = Book.objects.all()  # 1 query
for book in books:
    print(book.author.name)  # 1 query PER book! = N+1 queries
```

If there are 100 books → 101 database queries!

### Fix with `select_related` or `prefetch_related`

```python
# Fix — only 1 query total
books = Book.objects.select_related('author').all()
for book in books:
    print(book.author.name)  # No extra query!
```

---

## 11.4 select_related vs prefetch_related

### `select_related`
- For **ForeignKey** and **OneToOne** relationships.
- Does a SQL JOIN — fetches everything in one query.
- Best for single related objects.

```python
# ForeignKey: Book → Author
books = Book.objects.select_related('author').all()
```

### `prefetch_related`
- For **ManyToMany** and **reverse ForeignKey** relationships.
- Does separate queries and Python-joins them.
- Best for multiple related objects.

```python
# Reverse FK: Author → all their books
authors = Author.objects.prefetch_related('book_set').all()

# ManyToMany: Book → Tags
books = Book.objects.prefetch_related('tags').all()

# Chaining both
books = Book.objects.select_related('author').prefetch_related('tags').all()
```

### Quick Reference

| Situation | Use |
|-----------|-----|
| ForeignKey, OneToOne | `select_related` |
| ManyToMany, reverse FK | `prefetch_related` |
| Multiple relations | Chain both |

---

## 11.5 Redis Caching (Real-World)

### Simple Definition
Redis stores frequently accessed data in memory so the database doesn't have to be hit on every request.

### Setup

```bash
pip install django-redis
```

```python
# settings.py
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}
```

### Usage in Views

```python
from django.core.cache import cache

class BookListView(APIView):
    def get(self, request):
        # Try to get from cache first
        cached_books = cache.get('all_books')
        if cached_books:
            return Response(cached_books)

        # Not in cache — hit the database
        books = Book.objects.select_related('author').all()
        serializer = BookSerializer(books, many=True)
        data = serializer.data

        # Store in cache for 5 minutes
        cache.set('all_books', data, timeout=300)
        return Response(data)

    def post(self, request):
        ...
        cache.delete('all_books')  # Invalidate cache after write!
        return Response(...)
```

---

# 12. Testing

## 12.1 Django TestCase

### Simple Definition
`TestCase` is Django's built-in testing class. It wraps each test in a transaction and rolls it back after, keeping tests isolated.

```python
# tests/test_models.py
from django.test import TestCase
from apps.books.models import Book, Author

class BookModelTest(TestCase):
    def setUp(self):
        self.author = Author.objects.create(name="Alice", email="alice@test.com")

    def test_book_creation(self):
        book = Book.objects.create(title="Django Guide", author=self.author, price=299)
        self.assertEqual(book.title, "Django Guide")
        self.assertEqual(str(book), "Django Guide")

    def test_price_not_negative(self):
        # Test your custom validation
        book = Book.objects.create(title="Test", author=self.author, price=0)
        self.assertGreaterEqual(book.price, 0)
```

---

## 12.2 API Testing with DRF

```python
# tests/test_views.py
from rest_framework.test import APITestCase, APIClient
from rest_framework import status
from django.contrib.auth.models import User

class BookAPITest(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user('testuser', password='testpass')
        self.client.force_authenticate(user=self.user)
        self.author = Author.objects.create(name="Alice", email="alice@test.com")

    def test_list_books(self):
        response = self.client.get('/api/books/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)

    def test_create_book(self):
        data = {'title': 'New Book', 'author': self.author.id, 'price': '199.00'}
        response = self.client.post('/api/books/', data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(response.data['title'], 'New Book')

    def test_unauthenticated_access(self):
        self.client.logout()
        response = self.client.get('/api/books/')
        self.assertEqual(response.status_code, status.HTTP_401_UNAUTHORIZED)
```

---

## 12.3 pytest

### Simple Definition
`pytest` is a more powerful and concise testing tool than Django's built-in `TestCase`.

```bash
pip install pytest pytest-django
```

```ini
# pytest.ini
[pytest]
DJANGO_SETTINGS_MODULE = config.settings.development
python_files = tests/*.py
```

```python
# tests/test_books.py
import pytest
from rest_framework.test import APIClient
from django.contrib.auth.models import User

@pytest.fixture
def api_client():
    return APIClient()

@pytest.fixture
def authenticated_client(db):
    user = User.objects.create_user('user1', password='pass123')
    client = APIClient()
    client.force_authenticate(user=user)
    return client

@pytest.mark.django_db
def test_get_books(authenticated_client):
    response = authenticated_client.get('/api/books/')
    assert response.status_code == 200

@pytest.mark.django_db
def test_create_book(authenticated_client, author):
    data = {'title': 'pytest Book', 'author': author.id, 'price': '150.00'}
    response = authenticated_client.post('/api/books/', data)
    assert response.status_code == 201
```

---

## 12.4 Factory Boy

### Simple Definition
Factory Boy generates test data automatically. Instead of manually creating objects in every test, factories do it for you.

```bash
pip install factory-boy
```

```python
# factories.py
import factory
from django.contrib.auth.models import User
from apps.books.models import Author, Book

class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User

    username = factory.Sequence(lambda n: f'user{n}')
    email = factory.LazyAttribute(lambda obj: f'{obj.username}@test.com')
    password = factory.PostGenerationMethodCall('set_password', 'password123')

class AuthorFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Author

    name = factory.Faker('name')
    email = factory.Faker('email')

class BookFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Book

    title = factory.Faker('sentence', nb_words=3)
    author = factory.SubFactory(AuthorFactory)
    price = factory.Faker('pydecimal', left_digits=3, right_digits=2, positive=True)
```

```python
# In tests
@pytest.mark.django_db
def test_with_factory(authenticated_client):
    books = BookFactory.create_batch(5)  # Create 5 books instantly!
    response = authenticated_client.get('/api/books/')
    assert len(response.data) == 5
```

---

## 12.5 Postman / API Testing Collections

### Simple Definition
Postman is a GUI tool for manually testing APIs and creating automated test collections.

### Best Practices
- Create an **environment** for dev/prod URLs and tokens.
- Use **Collection Variables** for base URL, tokens.
- Write **Tests** in the Tests tab using JavaScript:

```javascript
// In Postman Tests tab
pm.test("Status is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Has results", () => {
    const data = pm.response.json();
    pm.expect(data.results).to.be.an('array');
});

// Auto-save token after login
const token = pm.response.json().access;
pm.environment.set("JWT_TOKEN", token);
```

---

# 13. Background Tasks

## 13.1 Celery (Deep)

### Simple Definition
Celery runs tasks outside the normal request-response cycle. Example: sending emails, generating PDFs, or processing images — without making the user wait.

### Why It Is Used
If a task takes 10 seconds, the user would wait 10 seconds for the API response. With Celery, the API returns instantly and the task runs in the background.

### Setup

```bash
pip install celery redis
```

```python
# config/celery.py
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings.development')

app = Celery('config')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()
```

```python
# config/__init__.py
from .celery import app as celery_app
__all__ = ['celery_app']
```

```python
# settings.py
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
```

```python
# apps/books/tasks.py
from celery import shared_task
from django.core.mail import send_mail

@shared_task
def send_welcome_email(user_email, username):
    send_mail(
        subject='Welcome!',
        message=f'Hi {username}, welcome to our platform!',
        from_email='noreply@example.com',
        recipient_list=[user_email],
    )

@shared_task
def generate_report(book_id):
    # Long-running task
    book = Book.objects.get(pk=book_id)
    # ... generate PDF report ...
    return f"Report for {book.title} generated"
```

```python
# views.py — call task
from .tasks import send_welcome_email

class RegisterView(APIView):
    def post(self, request):
        # ... create user ...
        send_welcome_email.delay(user.email, user.username)  # Runs in background!
        return Response({"message": "Registered successfully"}, status=201)
```

### Run Celery Worker

```bash
celery -A config worker --loglevel=info
```

---

## 13.2 Redis / RabbitMQ as Broker

### Simple Definition
The broker is the message queue between your Django app and Celery workers.

| Broker | Best For |
|--------|---------|
| **Redis** | Simple setup, also used for caching |
| **RabbitMQ** | Enterprise, complex routing, high reliability |

**Redis (recommended for most projects):**
```python
CELERY_BROKER_URL = 'redis://localhost:6379/0'
```

**RabbitMQ:**
```python
CELERY_BROKER_URL = 'amqp://guest:guest@localhost//'
```

---

## 13.3 Async Tasks (Emails, Payments)

### Celery Beat — Periodic Tasks

```bash
pip install django-celery-beat
```

```python
# settings.py
INSTALLED_APPS = ['django_celery_beat']

# Start the beat scheduler
# celery -A config beat --loglevel=info
```

```python
# tasks.py
from celery import shared_task

@shared_task
def send_daily_digest():
    # Run every day automatically
    users = User.objects.all()
    for user in users:
        send_digest_email.delay(user.id)

@shared_task
def process_payment(order_id, amount):
    # Call payment gateway
    # Update order status
    pass
```

**Schedule via code:**
```python
from celery.schedules import crontab

app.conf.beat_schedule = {
    'daily-digest': {
        'task': 'apps.users.tasks.send_daily_digest',
        'schedule': crontab(hour=8, minute=0),  # Every day at 8 AM
    },
}
```

---

# 14. API Documentation

## 14.1 Swagger / OpenAPI

### Simple Definition
Swagger (now called OpenAPI) is a standard format for describing APIs. It auto-generates interactive documentation where developers can browse and test endpoints.

### Why It Is Used
- Frontend devs know exactly what endpoints exist and what they expect.
- No need to manually write API docs.
- Interactive "Try it out" buttons in the browser.

---

## 14.2 drf-spectacular

### Simple Definition
`drf-spectacular` is the recommended DRF library for generating OpenAPI 3.0 docs. It's more modern than `drf-yasg`.

```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS = ['drf_spectacular']

REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'My API',
    'DESCRIPTION': 'API documentation for My Project',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
}
```

---

## 14.3 Auto API Docs Setup

```python
# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView, SpectacularRedocView

urlpatterns = [
    # Download schema
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    # Swagger UI
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
    # ReDoc UI (alternative)
    path('api/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]
```

Visit `/api/docs/` — interactive Swagger UI.

### Adding Custom Descriptions

```python
from drf_spectacular.utils import extend_schema

class BookListView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    @extend_schema(
        summary="List all books",
        description="Returns a paginated list of all available books.",
        responses={200: BookSerializer(many=True)}
    )
    def get(self, request, *args, **kwargs):
        return super().get(request, *args, **kwargs)

    @extend_schema(
        summary="Create a book",
        description="Creates a new book entry. Requires authentication."
    )
    def post(self, request, *args, **kwargs):
        return super().post(request, *args, **kwargs)
```

---

*End of Django REST Framework Complete Notes*
