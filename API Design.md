# Django REST Framework — Complete Notes
> REST API Best Practices: 7 Essential Concepts

---

## Table of Contents

1. [Resource Base Endpoint](#1-resource-base-endpoint)
   - [Definition](#11-definition)
   - [Why Use It](#12-why-use-it)
   - [Types](#13-types)
   - [Advantages & Disadvantages](#14-advantages--disadvantages)
   - [Tips & Tricks](#15-tips--tricks)
   - [Best Practices](#16-best-practices)
   - [DRF Setup & Code](#17-drf-setup--code)

2. [Correct HTTP Methods](#2-correct-http-methods)
   - [Definition](#21-definition)
   - [Why Use It](#22-why-use-it)
   - [Types](#23-types)
   - [Advantages & Disadvantages](#24-advantages--disadvantages)
   - [Tips & Tricks](#25-tips--tricks)
   - [Best Practices](#26-best-practices)
   - [DRF Setup & Code](#27-drf-setup--code)

3. [Proper Status Codes](#3-proper-status-codes)
   - [Definition](#31-definition)
   - [Why Use It](#32-why-use-it)
   - [Types](#33-types)
   - [Advantages & Disadvantages](#34-advantages--disadvantages)
   - [Tips & Tricks](#35-tips--tricks)
   - [Best Practices](#36-best-practices)
   - [DRF Setup & Code](#37-drf-setup--code)

4. [Pagination](#4-pagination)
   - [Definition](#41-definition)
   - [Why Use It](#42-why-use-it)
   - [Types](#43-types)
   - [Advantages & Disadvantages](#44-advantages--disadvantages)
   - [Tips & Tricks](#45-tips--tricks)
   - [Best Practices](#46-best-practices)
   - [DRF Setup & Code](#47-drf-setup--code)

5. [Versioning](#5-versioning)
   - [Definition](#51-definition)
   - [Why Use It](#52-why-use-it)
   - [Types](#53-types)
   - [Advantages & Disadvantages](#54-advantages--disadvantages)
   - [Tips & Tricks](#55-tips--tricks)
   - [Best Practices](#56-best-practices)
   - [DRF Setup & Code](#57-drf-setup--code)

6. [Consistent Response Structure](#6-consistent-response-structure)
   - [Definition](#61-definition)
   - [Why Use It](#62-why-use-it)
   - [Types](#63-types)
   - [Advantages & Disadvantages](#64-advantages--disadvantages)
   - [Tips & Tricks](#65-tips--tricks)
   - [Best Practices](#66-best-practices)
   - [DRF Setup & Code](#67-drf-setup--code)

7. [Authentication](#7-authentication)
   - [Definition](#71-definition)
   - [Why Use It](#72-why-use-it)
   - [Types](#73-types)
   - [Advantages & Disadvantages](#74-advantages--disadvantages)
   - [Tips & Tricks](#75-tips--tricks)
   - [Best Practices](#76-best-practices)
   - [DRF Setup & Code](#77-drf-setup--code)

---

---

# 1. Resource Base Endpoint

## 1.1 Definition

A **resource** is any real-world thing your API exposes — like a User, a Book, or an Order.
A **base endpoint** is the URL path where you access that resource.

Think of it like a folder on a server. `/api/books/` is the "books folder" — everything about books lives at this address.

> **Core Rule:** Your URL should be a **noun** (the thing), not a verb (the action). The action comes from the HTTP method.

| Good (noun) | Bad (verb) |
|---|---|
| `/api/books/` | `/api/getBooks/` |
| `/api/users/` | `/api/createUser/` |
| `/api/orders/5/` | `/api/deleteOrder/5/` |

---

## 1.2 Why Use It

Without a consistent URL structure, your API becomes confusing. Different developers might write `/getUser`, `/fetchUser`, `/user_data` — all doing the same thing.

Resource-based endpoints give your API a **predictable address** for every entity. Frontend devs, mobile apps, and third-party services all know exactly where to look.

- Real-world analogy: A post office has one address. You go there for all mail needs. Same idea — one URL per resource.

---

## 1.3 Types

| Type | URL Pattern | Purpose |
|---|---|---|
| Collection | `/api/books/` | List all books or create a new one |
| Detail | `/api/books/5/` | Single resource accessed by ID |
| Nested | `/api/authors/3/books/` | Books belonging to a specific author |
| Action | `/api/books/5/publish/` | Custom action on a specific resource |

---

## 1.4 Advantages & Disadvantages

**Advantages:**
- Clean, readable, and self-documenting URLs
- Follows REST standards — universally understood
- Easy to version (just add `/v1/` prefix)
- Works seamlessly with DRF's `DefaultRouter`

**Disadvantages:**
- Nested resources can get very deep and confusing
- Complex filtering queries may not map cleanly to a resource URL
- Requires discipline and naming conventions across the team

---

## 1.5 Tips & Tricks

- Always use **plural nouns**: `/books/` not `/book/`
- Keep nesting shallow — max 2 levels deep: `/authors/3/books/` is fine; `/authors/3/books/5/chapters/2/` is too deep
- Never put the HTTP method in the URL like `/getBooks` — the method belongs in the HTTP verb
- Use hyphens for multi-word resources: `/blog-posts/` not `/blogPosts/` or `/blog_posts/`

---

## 1.6 Best Practices

- Use lowercase letters only in URLs
- Use hyphens (`-`) not underscores (`_`) for multi-word paths
- Keep trailing slash consistent — DRF uses trailing slash by default
- Group all endpoints under a common prefix: `/api/`
- Prefix version in the URL: `/api/v1/books/`

---

## 1.7 DRF Setup & Code

**Step-by-step:** Install DRF → Create model → Create serializer → Create view → Register URL

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]

# models.py
from django.db import models

class Book(models.Model):
    title  = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    price  = models.DecimalField(max_digits=6, decimal_places=2)

    def __str__(self):
        return self.title


# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model  = Book
        fields = ['id', 'title', 'author', 'price']


# views.py
from rest_framework import viewsets
from .models import Book
from .serializers import BookSerializer

class BookViewSet(viewsets.ModelViewSet):
    queryset         = Book.objects.all()
    serializer_class = BookSerializer


# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register(r'books', BookViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]

# DefaultRouter auto-generates these endpoints:
# GET    /api/books/     → list all books
# POST   /api/books/     → create a new book
# GET    /api/books/5/   → retrieve book with id=5
# PUT    /api/books/5/   → full update of book id=5
# PATCH  /api/books/5/   → partial update of book id=5
# DELETE /api/books/5/   → delete book id=5
```

---

---

# 2. Correct HTTP Methods

## 2.1 Definition

HTTP methods tell the server **what action** you want to perform on a resource. The URL says *where*, the method says *what*.

| Method | Action | SQL Equivalent |
|---|---|---|
| `GET` | Fetch / Read data | `SELECT` |
| `POST` | Create new data | `INSERT` |
| `PUT` | Replace entire record | `UPDATE` (all fields) |
| `PATCH` | Update partial record | `UPDATE` (some fields) |
| `DELETE` | Remove a record | `DELETE` |

---

## 2.2 Why Use It

Using wrong HTTP methods causes bugs, security issues, and breaks REST standards.

- Browsers **cache GET** requests but not POST
- Search engines and logs **index GET** requests
- Proxies and CDNs treat different methods differently
- Using POST for everything loses caching, semantics, and browser history

---

## 2.3 Types

| Method | Safe? | Idempotent? | Has Request Body? | Use Case |
|---|---|---|---|---|
| GET | Yes | Yes | No | Read data |
| POST | No | No | Yes | Create a resource |
| PUT | No | Yes | Yes | Full update (all fields required) |
| PATCH | No | Yes | Yes | Partial update (only changed fields) |
| DELETE | No | Yes | No | Delete a resource |
| HEAD | Yes | Yes | No | Check if resource exists (no body) |
| OPTIONS | Yes | Yes | No | Check allowed methods on endpoint |

**Safe** = won't change server data.
**Idempotent** = calling it 10 times gives the same result as calling it once.

---

## 2.4 Advantages & Disadvantages

**Advantages:**
- Semantic clarity — each method has a clear meaning
- GET requests are automatically cacheable by browsers and CDNs
- Standard behavior understood by all HTTP clients
- DRF's `ModelViewSet` maps methods to actions automatically

**Disadvantages:**
- Some older firewalls and proxies block PUT and DELETE
- PUT vs PATCH distinction confuses beginners
- Some legacy HTML forms only support GET and POST

---

## 2.5 Tips & Tricks

- **PUT vs PATCH:** PUT requires ALL fields to be sent. PATCH only needs the fields you want to change. Always prefer PATCH for updates — safer and simpler for clients.
- Never use GET to create or delete data. GET requests can be cached, bookmarked, and logged — accidental deletes from cache would be catastrophic.
- DRF's `ModelViewSet` automatically maps all standard methods. Use `http_method_names = ['get', 'post']` to restrict which methods are allowed.
- Use the `@action` decorator for custom non-CRUD operations like `/books/5/publish/`.

---

## 2.6 Best Practices

- Use GET for all read-only operations, never for mutations
- Use POST to create new resources, not to update
- Prefer PATCH over PUT — update only what changed
- Use DELETE to remove resources; return `204 No Content` on success
- Use `@action(detail=True, methods=['post'])` for custom resource actions

---

## 2.7 DRF Setup & Code

```python
# views.py — ModelViewSet handles all standard methods automatically
from rest_framework import viewsets, status
from rest_framework.decorators import action, api_view
from rest_framework.response import Response

class BookViewSet(viewsets.ModelViewSet):
    queryset         = Book.objects.all()
    serializer_class = BookSerializer

    # Custom action: POST /api/books/5/publish/
    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        book = self.get_object()
        book.is_published = True
        book.save()
        return Response({'status': 'published'})

    # Custom action: GET /api/books/featured/
    @action(detail=False, methods=['get'])
    def featured(self, request):
        featured_books = Book.objects.filter(is_featured=True)
        serializer = self.get_serializer(featured_books, many=True)
        return Response(serializer.data)


# Read-only ViewSet — only allows GET (list + retrieve)
class ReadOnlyBookViewSet(viewsets.ReadOnlyModelViewSet):
    queryset         = Book.objects.all()
    serializer_class = BookSerializer


# Function-based view with explicit method control
@api_view(['GET', 'POST'])  # only these methods allowed
def book_list(request):
    if request.method == 'GET':
        books      = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

---

---

# 3. Proper Status Codes

## 3.1 Definition

Status codes are 3-digit numbers the server sends back to tell the client **what happened** with their request. They are the server's way of communicating the outcome.

| Range | Category | Meaning |
|---|---|---|
| `1xx` | Informational | Request received, still processing |
| `2xx` | Success | Request worked correctly |
| `3xx` | Redirection | Go somewhere else |
| `4xx` | Client Error | You sent something wrong |
| `5xx` | Server Error | Something broke on the server |

---

## 3.2 Why Use It

Status codes let clients (apps, browsers, frontend code) know exactly what happened **without parsing the response body**. A `404` means "not found" — no extra message parsing needed.

> Always returning `200` with an error in the body is bad practice — clients can't distinguish success from failure without reading every response body manually.

---

## 3.3 Types

**Most important status codes for REST APIs:**

| Code | Name | When to Use |
|---|---|---|
| `200` | OK | Successful GET, PUT, PATCH |
| `201` | Created | POST successfully created a resource |
| `204` | No Content | DELETE success — no body to return |
| `400` | Bad Request | Validation error, missing required fields |
| `401` | Unauthorized | Not authenticated — no token or invalid token |
| `403` | Forbidden | Authenticated but does not have permission |
| `404` | Not Found | Requested resource does not exist |
| `405` | Method Not Allowed | HTTP method not supported on this endpoint |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Unhandled exception on server |

---

## 3.4 Advantages & Disadvantages

**Advantages:**
- Universal language — every HTTP client understands status codes
- Enables proper error handling in frontend code
- DRF raises the correct codes by default for most cases
- Improves API observability and monitoring

**Disadvantages:**
- Easy to accidentally use wrong codes (e.g., returning 200 for errors)
- Some codes are rarely used or misunderstood by junior developers
- No strict enforcement — requires team discipline

---

## 3.5 Tips & Tricks

- **401 vs 403:** `401` = "Who are you?" (not authenticated). `403` = "I know who you are, but you can't do this" (no permission).
- Always use DRF's `status` module — never hardcode numbers. Write `status.HTTP_404_NOT_FOUND` not `404`. Far more readable and self-documenting.
- Never return `200` with `{"success": false}` in the body — use the correct error code (`400`, `404`, etc.).
- DRF raises `404` automatically when using `get_object_or_404()`.

---

## 3.6 Best Practices

- Return `201` after a successful POST creation
- Return `204` after a successful DELETE (no body needed)
- Return `400` with field-level validation errors from the serializer
- Let DRF handle `404` automatically via `get_object_or_404`
- Never expose raw `500` errors in production — log them server-side, return a friendly message
- Always use `from rest_framework import status` — use named constants

---

## 3.7 DRF Setup & Code

```python
# views.py
from rest_framework import status
from rest_framework.response import Response
from rest_framework.decorators import api_view
from django.shortcuts import get_object_or_404
from .models import Book
from .serializers import BookSerializer

@api_view(['GET', 'POST'])
def book_list(request):
    if request.method == 'GET':
        books      = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    elif request.method == 'POST':
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)  # 201, not 200
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PATCH', 'DELETE'])
def book_detail(request, pk):
    book = get_object_or_404(Book, pk=pk)  # auto 404 if not found

    if request.method == 'GET':
        serializer = BookSerializer(book)
        return Response(serializer.data, status=status.HTTP_200_OK)

    elif request.method == 'PATCH':
        serializer = BookSerializer(book, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        book.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)  # no body!


# Custom exception handler — adds status_code to every error response
# exceptions.py
from rest_framework.views import exception_handler

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)
    if response is not None:
        response.data['status_code'] = response.status_code
    return response

# Register in settings.py:
# REST_FRAMEWORK = {
#     'EXCEPTION_HANDLER': 'myapp.exceptions.custom_exception_handler'
# }
```

---

---

# 4. Pagination

## 4.1 Definition

Pagination means splitting a large list of results into smaller pages. Instead of returning 10,000 records at once, you return 20 at a time.

Think of Google Search — you get page 1 of results, then page 2. The same concept applies to your API list endpoints.

---

## 4.2 Why Use It

Returning too many records at once causes slow responses, high memory usage, and a bad user experience.

| Without Pagination | With Pagination |
|---|---|
| All 10,000 rows sent on every request | Only 20 rows per request |
| Slow network transfer | Fast response times |
| Frontend may crash or lag | Smooth user experience |
| Server memory spike | Low, stable server memory |

---

## 4.3 Types

| Type | URL Example | Best For |
|---|---|---|
| `PageNumberPagination` | `?page=2&page_size=10` | Simple page numbers (Google-style) |
| `LimitOffsetPagination` | `?limit=10&offset=20` | Skip N records (SQL-style) |
| `CursorPagination` | `?cursor=abc123` | Real-time feeds, no page jumping |

- **PageNumber** is the easiest to understand and implement.
- **LimitOffset** is flexible but slow on very large datasets.
- **Cursor** is the most performant for large datasets but clients cannot jump to page 50.

---

## 4.4 Advantages & Disadvantages

**Advantages:**
- Massive performance improvement on large datasets
- Reduces server load and database query time
- Better user experience (faster API responses)
- DRF has three pagination types built-in — no extra libraries needed

**Disadvantages:**
- Cursor pagination cannot jump to a specific page number
- Offset-based pagination becomes slow on very large tables (millions of rows)
- Frontend needs additional logic to handle pagination links

---

## 4.5 Tips & Tricks

- Always set `max_page_size` to prevent clients from requesting 99,999 records: `max_page_size = 100`
- DRF's paginated response always includes: `count` (total records), `next` (URL), `previous` (URL), `results` (data array)
- Avoid `LimitOffset` pagination on tables with millions of rows — `OFFSET 1000000` in SQL is very slow; use `CursorPagination` instead
- You can override `get_paginated_response` to add custom fields to the pagination metadata

---

## 4.6 Best Practices

- Always paginate list endpoints — never return unbounded lists
- Set a sensible default page size (10–25 records)
- Set a maximum page size cap to prevent abuse
- Use cursor pagination for large, real-time, or constantly-updated datasets
- Include total count in response so frontend can show "Page X of Y"
- Apply pagination globally in `settings.py`, then override per-view where needed

---

## 4.7 DRF Setup & Code

```python
# pagination.py
from rest_framework.pagination import (
    PageNumberPagination,
    LimitOffsetPagination,
    CursorPagination
)

# Option 1: Page Number Pagination (most common)
class StandardPagination(PageNumberPagination):
    page_size             = 10
    page_size_query_param = 'page_size'  # client can override with ?page_size=5
    max_page_size         = 100
    # Usage: GET /api/books/?page=2&page_size=5

# Option 2: Limit Offset Pagination
class BookLimitOffsetPagination(LimitOffsetPagination):
    default_limit = 10
    max_limit     = 100
    # Usage: GET /api/books/?limit=10&offset=20

# Option 3: Cursor Pagination (best for real-time feeds)
class BookCursorPagination(CursorPagination):
    page_size = 10
    ordering  = '-created_at'  # must specify ordering field
    # Usage: GET /api/books/?cursor=cD0yMDIz...


# Apply globally in settings.py:
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'myapp.pagination.StandardPagination',
    'PAGE_SIZE': 10,
}

# Or apply per-view:
class BookViewSet(viewsets.ModelViewSet):
    queryset         = Book.objects.all()
    serializer_class = BookSerializer
    pagination_class = StandardPagination  # overrides global setting


# Customise the paginated response shape:
class StandardPagination(PageNumberPagination):
    page_size = 10

    def get_paginated_response(self, data):
        return Response({
            'total':    self.page.paginator.count,
            'next':     self.get_next_link(),
            'previous': self.get_previous_link(),
            'results':  data
        })

# Example response:
# {
#   "total": 150,
#   "next": "http://api.example.com/api/books/?page=3",
#   "previous": "http://api.example.com/api/books/?page=1",
#   "results": [ {...}, {...} ]
# }
```

---

---

# 5. Versioning

## 5.1 Definition

Versioning means labelling your API with a version number so you can **change it without breaking existing users**.

Think of it like software releases — your old app keeps working with v1 while new clients can use v2 with new features.

```
/api/v1/books/   →  old response format (existing clients)
/api/v2/books/   →  new response format (new clients)
```

---

## 5.2 Why Use It

APIs are contracts with clients. If you change the response format, field names, or structure, old clients break. Versioning lets you evolve your API without forcing everyone to update at the same time.

> Real example: Twitter changed their API from v1 to v2. Many third-party apps kept running on v1 until they were ready to migrate on their own schedule.

---

## 5.3 Types

| Type | Example | DRF Class |
|---|---|---|
| URL Path | `/api/v1/books/` | `URLPathVersioning` |
| Query Parameter | `/api/books/?version=1` | `QueryParameterVersioning` |
| Accept Header | `Accept: application/json; version=1` | `AcceptHeaderVersioning` |
| Hostname | `v1.api.example.com` | `HostNameVersioning` |
| URL Namespace | Django URL namespace based | `NamespaceVersioning` |

**URL Path versioning** is the most popular — easy to read, test in a browser, and document.

---

## 5.4 Advantages & Disadvantages

**Advantages:**
- Backward compatibility — old clients keep working
- Safe to introduce breaking changes in new versions
- Multiple clients can use different API versions simultaneously
- Versioning support is built into DRF — no extra libraries

**Disadvantages:**
- Maintaining multiple versions requires extra work
- Risk of code duplication across versions
- Old versions must eventually be deprecated and removed

---

## 5.5 Tips & Tricks

- Access the current version inside any view with `request.version` — DRF sets it automatically
- Use a single ViewSet and branch logic by version: `if request.version == 'v2':` — avoids code duplication
- Return a different serializer per version using `get_serializer_class()` — clean separation
- Don't version too early! Start with v1 and only create v2 when you have an actual breaking change

---

## 5.6 Best Practices

- Version your API from day one — adding versioning later is difficult
- Use URL path versioning for public APIs (most readable)
- Announce deprecation of old versions well in advance (minimum 6 months)
- Document each version clearly and separately
- Never remove or rename fields within an existing version — only add in new versions
- Keep backward compatibility within a version (additive changes only)

---

## 5.7 DRF Setup & Code

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION':          'v1',
    'ALLOWED_VERSIONS':         ['v1', 'v2'],
    'VERSION_PARAM':            'version',
}

# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register(r'books', BookViewSet)

urlpatterns = [
    path('api/<version>/', include(router.urls)),
]
# Results in:
# /api/v1/books/
# /api/v2/books/

# serializers.py — separate serializer per version
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model  = Book
        fields = ['id', 'title', 'author', 'price']  # v1

class BookSerializerV2(serializers.ModelSerializer):
    class Meta:
        model  = Book
        fields = ['id', 'title', 'author', 'price', 'published_at', 'category']  # v2 adds fields

# views.py — use request.version to serve different data
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()

    def get_serializer_class(self):
        # Return different serializer based on version
        if self.request.version == 'v2':
            return BookSerializerV2
        return BookSerializer  # default v1

    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        # v2 only returns published books
        if request.version == 'v2':
            queryset = queryset.filter(is_published=True)
        serializer = self.get_serializer(queryset, many=True)
        return Response(serializer.data)
```

---

---

# 6. Consistent Response Structure

## 6.1 Definition

Every API endpoint should return data in the **same shape**. Whether fetching books or users, success or error — the response structure always looks the same.

**Inconsistent (bad):**
```json
// Endpoint 1: {"data": [...]}
// Endpoint 2: {"books": [...]}
// Endpoint 3: [...]
```

**Consistent (good):**
```json
{
  "success": true,
  "message": "Books retrieved successfully",
  "data": [...],
  "errors": null
}
```

---

## 6.2 Why Use It

Frontend developers can write **one generic API handler** when the structure is always the same. With inconsistent responses, they need custom code for every single endpoint.

> It's like receiving packages: if every box always has the sender/receiver label in the same place, unpacking becomes automatic. Inconsistency forces manual inspection every time.

---

## 6.3 Types

| Pattern | Fields | Used By |
|---|---|---|
| JSend | `status`, `data`, `message` | Small to medium APIs |
| JSON:API | `data`, `errors`, `meta`, `links` | Large enterprise APIs |
| Custom | `success`, `data`, `message`, `errors` | Most DRF projects |

Most teams use a simple custom format — a `success` flag, `data` payload, human-readable `message`, and `errors` detail.

---

## 6.4 Advantages & Disadvantages

**Advantages:**
- Frontend can write one generic response handler
- Easier to debug — same structure every time
- Self-documenting — clients always know where the data is
- Better error reporting with consistent error field structure

**Disadvantages:**
- Extra wrapper object adds a small amount of overhead
- Must implement a custom renderer or helper
- May require overriding DRF's default response behavior

---

## 6.5 Tips & Tricks

- Override DRF's `Response` or use a custom renderer to wrap every response **automatically** — no need to manually wrap in every view
- Always include these four fields: `success` (bool), `data` (payload or null), `message` (human-readable string), `errors` (validation details or null)
- Don't nest data too deep — `response.data.user.profile.address` is painful. Keep it as flat as possible
- Never return a raw list `[...]` — always wrap in an object so you can add metadata later without a breaking change

---

## 6.6 Best Practices

- Define your response schema on day 1 and stick to it throughout the project
- Use a shared helper function or custom renderer for automatic wrapping
- Always return `null` for missing optional fields — never omit the key entirely
- Include metadata (timestamps, pagination info) in a separate `meta` key
- Include `errors` as a dictionary with field-level detail for validation failures

---

## 6.7 DRF Setup & Code

```python
# utils/response.py — reusable response helpers
from rest_framework.response import Response
from rest_framework import status

def success_response(data=None, message="Success", status_code=status.HTTP_200_OK):
    return Response({
        "success": True,
        "message": message,
        "data":    data,
        "errors":  None
    }, status=status_code)

def error_response(errors=None, message="Error occurred", status_code=status.HTTP_400_BAD_REQUEST):
    return Response({
        "success": False,
        "message": message,
        "data":    None,
        "errors":  errors
    }, status=status_code)


# views.py — use helpers in every view
from .utils.response import success_response, error_response

class BookViewSet(viewsets.ModelViewSet):
    queryset         = Book.objects.all()
    serializer_class = BookSerializer

    def list(self, request):
        books      = self.get_queryset()
        serializer = BookSerializer(books, many=True)
        return success_response(
            data=serializer.data,
            message="Books retrieved successfully"
        )

    def create(self, request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return success_response(
                data=serializer.data,
                message="Book created successfully",
                status_code=status.HTTP_201_CREATED
            )
        return error_response(
            errors=serializer.errors,
            message="Validation failed"
        )

    def destroy(self, request, pk=None):
        book = get_object_or_404(Book, pk=pk)
        book.delete()
        return success_response(
            data=None,
            message="Book deleted successfully",
            status_code=status.HTTP_204_NO_CONTENT
        )


# Example success response:
# {
#   "success": true,
#   "message": "Books retrieved successfully",
#   "data": [{"id": 1, "title": "Django Pro", ...}],
#   "errors": null
# }

# Example error response:
# {
#   "success": false,
#   "message": "Validation failed",
#   "data": null,
#   "errors": {"title": ["This field is required."]}
# }
```

---

---

# 7. Authentication

## 7.1 Definition

**Authentication** = "Who are you?" — verifying the identity of the client before granting access to your API.

**Authentication vs Authorization — key difference:**

| Concept | Question | Example |
|---|---|---|
| Authentication | Who are you? | Login with username + password |
| Authorization (Permissions) | What are you allowed to do? | Only admins can delete books |

---

## 7.2 Why Use It

Without authentication, anyone on the internet can read, create, or delete your data. Every real-world API must protect sensitive data and actions.

- **Public endpoints** (product catalog, blog posts) don't need auth
- **Private endpoints** (user profile, orders, admin actions) must always require auth

---

## 7.3 Types

| Type | How It Works | Best For |
|---|---|---|
| Session Authentication | Django session cookie | Traditional browser-based apps |
| Token Authentication | `Authorization: Token abc123` header | Mobile apps, SPAs |
| JWT Authentication | Signed token, no DB lookup needed | Scalable APIs, microservices |
| Basic Authentication | `username:password` encoded in header | Testing and development only |
| OAuth2 | Third-party login delegation | Social login (Google, GitHub) |

**JWT (JSON Web Tokens)** is the most popular choice for modern REST APIs.

---

## 7.4 Advantages & Disadvantages

| | Token Auth | JWT Auth |
|---|---|---|
| **Pros** | Simple, built into DRF | No DB lookup, has expiry, works across services |
| **Cons** | Stored in DB (slower at scale), no expiry | Cannot invalidate without a blacklist, larger size |

---

## 7.5 Tips & Tricks

- **JWT gives you two tokens:**
  - `access token` — short-lived (5–15 minutes), used for API requests
  - `refresh token` — long-lived (7 days), used to get a new access token
- Use `IsAuthenticatedOrReadOnly` permission to allow public GET but require auth for POST/PUT/DELETE
- Store tokens in `httpOnly` cookies — **never** in `localStorage` (vulnerable to XSS attacks)
- `simplejwt` library handles all JWT logic — no need to write it manually

---

## 7.6 Best Practices

- Use JWT (`djangorestframework-simplejwt`) for all modern APIs
- Always use HTTPS — never send tokens over plain HTTP
- Set short expiry on access tokens (5–15 minutes)
- Use refresh tokens for long-lived user sessions
- Never log or print tokens anywhere in your code
- Use `permission_classes` on every view to explicitly control access
- Rotate refresh tokens so each use generates a new one

---

## 7.7 DRF Setup & Code

```python
# Step 1: Install the library
# pip install djangorestframework-simplejwt

# settings.py
from datetime import timedelta

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME':  timedelta(minutes=15),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS':  True,   # each refresh gets a new refresh token
    'BLACKLIST_AFTER_ROTATION': True, # old refresh tokens are blacklisted
}


# urls.py
from django.urls import path, include
from rest_framework_simplejwt.views import (
    TokenObtainPairView,  # POST: login → returns access + refresh tokens
    TokenRefreshView,     # POST: refresh → returns new access token
    TokenVerifyView,      # POST: verify → confirms token is valid
)

urlpatterns = [
    path('api/token/',         TokenObtainPairView.as_view()),
    path('api/token/refresh/', TokenRefreshView.as_view()),
    path('api/token/verify/',  TokenVerifyView.as_view()),
    path('api/',               include(router.urls)),
]


# views.py — granular permission control per action
from rest_framework.permissions import IsAuthenticated, IsAdminUser, AllowAny

class BookViewSet(viewsets.ModelViewSet):
    queryset         = Book.objects.all()
    serializer_class = BookSerializer

    def get_permissions(self):
        if self.action in ['list', 'retrieve']:
            return [AllowAny()]        # public — no login required
        elif self.action == 'destroy':
            return [IsAdminUser()]     # only admin users can delete
        return [IsAuthenticated()]     # all other actions require login


# How to use JWT in a client:
# Step 1: Login
#   POST /api/token/
#   Body: { "username": "alice", "password": "pass123" }
#   Response: { "access": "eyJ...", "refresh": "eyJ..." }
#
# Step 2: Make authenticated requests
#   Add header: Authorization: Bearer eyJ...
#
# Step 3: Refresh when access token expires
#   POST /api/token/refresh/
#   Body: { "refresh": "eyJ..." }
#   Response: { "access": "eyJ..." }  ← new access token
```

---

---

## Quick Reference Cheat Sheet

### URL Design

```
GET    /api/v1/books/        → list all books
POST   /api/v1/books/        → create a new book
GET    /api/v1/books/5/      → get book with id=5
PATCH  /api/v1/books/5/      → partial update book id=5
PUT    /api/v1/books/5/      → full update book id=5
DELETE /api/v1/books/5/      → delete book id=5
POST   /api/v1/books/5/publish/  → custom action
```

### HTTP Method → Status Code Map

| Action | Method | Success Code | Error Code |
|---|---|---|---|
| List records | GET | 200 | 404 |
| Get one record | GET | 200 | 404 |
| Create record | POST | 201 | 400 |
| Full update | PUT | 200 | 400, 404 |
| Partial update | PATCH | 200 | 400, 404 |
| Delete record | DELETE | 204 | 404 |
| Not logged in | any | — | 401 |
| No permission | any | — | 403 |

### DRF Settings Template

```python
# settings.py — complete REST_FRAMEWORK config
REST_FRAMEWORK = {
    # Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    # Permissions
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    # Pagination
    'DEFAULT_PAGINATION_CLASS': 'myapp.pagination.StandardPagination',
    'PAGE_SIZE': 10,
    # Versioning
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION':          'v1',
    'ALLOWED_VERSIONS':         ['v1', 'v2'],
    # Exception handler
    'EXCEPTION_HANDLER': 'myapp.exceptions.custom_exception_handler',
}
```

---

*Notes generated from the DRF Best Practices Interactive Guide.*
*Topics: Resource Endpoints · HTTP Methods · Status Codes · Pagination · Versioning · Response Structure · Authentication*
