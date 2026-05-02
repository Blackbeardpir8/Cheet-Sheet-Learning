# Django REST Framework — Choice Fields: Complete Notes

## Table of Contents

1. [Model Level](#1-model-level)
   - 1.1 [What is a Choice Field?](#11-what-is-a-choice-field)
   - 1.2 [Defining Choices Using TextChoices](#12-defining-choices-using-textchoices)
   - 1.3 [Value vs Label — The Critical Difference](#13-value-vs-label--the-critical-difference)
   - 1.4 [IntegerChoices (Bonus)](#14-integerchoices-bonus)
   - 1.5 [Tips and Best Practices at Model Level](#15-tips-and-best-practices-at-model-level)

2. [Serializer Level](#2-serializer-level)
   - 2.1 [Basic Serializer for Choice Field](#21-basic-serializer-for-choice-field)
   - 2.2 [Showing the Display Label using get_FOO_display](#22-showing-the-display-label-using-get_foo_display)
   - 2.3 [Sending All Available Choices using SerializerMethodField](#23-sending-all-available-choices-using-serializermethodfield)
   - 2.4 [Best Structure for Choice Data (Frontend-Friendly)](#24-best-structure-for-choice-data-frontend-friendly)
   - 2.5 [Advantages and Disadvantages](#25-advantages-and-disadvantages)
   - 2.6 [Tips and Best Practices at Serializer Level](#26-tips-and-best-practices-at-serializer-level)

3. [View Level (APIView)](#3-view-level-apiview)
   - 3.1 [What is APIView?](#31-what-is-apiview)
   - 3.2 [GET API — Sending Choices Inline in Response](#32-get-api--sending-choices-inline-in-response)
   - 3.3 [Separate Meta API Endpoint for Choices](#33-separate-meta-api-endpoint-for-choices)
   - 3.4 [When to Use Each Approach](#34-when-to-use-each-approach)
   - 3.5 [URL Configuration](#35-url-configuration)
   - 3.6 [Tips and Best Practices at View Level](#36-tips-and-best-practices-at-view-level)

4. [API Usage (Postman / Hoppscotch)](#4-api-usage-postman--hoppscotch)
   - 4.1 [How Frontend Fetches Choices](#41-how-frontend-fetches-choices)
   - 4.2 [How to Send Selected Choice in POST / PUT / PATCH](#42-how-to-send-selected-choice-in-post--put--patch)
   - 4.3 [Common Mistakes](#43-common-mistakes)
   - 4.4 [Complete Request-Response Examples](#44-complete-request-response-examples)

5. [Best Practices](#5-best-practices)
   - 5.1 [Clean Architecture Approach](#51-clean-architecture-approach)
   - 5.2 [Why Separate Meta APIs are Preferred](#52-why-separate-meta-apis-are-preferred)
   - 5.3 [How Large-Scale Projects Handle Dynamic Choices](#53-how-large-scale-projects-handle-dynamic-choices)

6. [Complete Working Project Example](#6-complete-working-project-example)

---

## 1. Model Level

### 1.1 What is a Choice Field?

**Simple definition:**
A choice field is a field in your database that only allows certain specific values — like a dropdown. Instead of letting someone type anything, you restrict the options.

**Real-world example:**
Imagine an `Order` model. The order status can only be one of: `pending`, `processing`, `shipped`, `delivered`, or `cancelled`. You don't want someone accidentally storing `"done"` or `"complete"` — you want strict control.

**Why use it?**
- Prevents invalid data in your database
- Makes your code self-documenting — all valid values are defined in one place
- Django validates the value automatically before saving

---

### 1.2 Defining Choices Using TextChoices

**TextChoices** is a Django built-in class (Django 3.0+) that lets you define choices cleanly using Python enums.

```python
# models.py

from django.db import models


class Order(models.Model):

    # Step 1: Define your choices INSIDE the model using TextChoices
    class StatusChoices(models.TextChoices):
        PENDING    = 'pending',    'Pending'       # value, label
        PROCESSING = 'processing', 'Processing'
        SHIPPED    = 'shipped',    'Shipped'
        DELIVERED  = 'delivered',  'Delivered'
        CANCELLED  = 'cancelled',  'Cancelled'

    # Step 2: Use it in your field
    customer_name = models.CharField(max_length=200)
    total_amount  = models.DecimalField(max_digits=10, decimal_places=2)

    status = models.CharField(
        max_length=20,
        choices=StatusChoices.choices,   # Attach choices here
        default=StatusChoices.PENDING    # Default value
    )

    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"Order #{self.id} — {self.customer_name}"
```

**Explanation:**
- `StatusChoices` is a class inside `Order` (inner class — completely fine and recommended)
- Each line inside `StatusChoices` is: `CONSTANT_NAME = 'db_value', 'Human Readable Label'`
- `StatusChoices.choices` returns: `[('pending', 'Pending'), ('processing', 'Processing'), ...]`
- Django stores only the **value** (`'pending'`) in the database

---

### 1.3 Value vs Label — The Critical Difference

This is the most important concept to understand first.

| Term | What It Is | Where It Lives | Example |
|------|-----------|----------------|---------|
| **Value** | What gets stored in DB | Database column | `'pending'` |
| **Label** | Human-readable display text | Python only | `'Pending'` |

```python
# Django shell demo — run: python manage.py shell

from orders.models import Order

# Accessing value (stored in DB)
print(Order.StatusChoices.PENDING.value)   # → 'pending'

# Accessing label (human text)
print(Order.StatusChoices.PENDING.label)   # → 'Pending'

# All choices as list of tuples
print(Order.StatusChoices.choices)
# → [('pending', 'Pending'), ('processing', 'Processing'), ...]

# Creating an order
order = Order.objects.create(
    customer_name="Ali Khan",
    total_amount=1500.00,
    status=Order.StatusChoices.PENDING   # Stores 'pending' in DB
)

# Fetching the label from an instance
print(order.get_status_display())   # → 'Pending'  ← Django magic method
```

**Key rule:** Always store and send the **value** (`'pending'`), never the label (`'Pending'`).

---

### 1.4 IntegerChoices (Bonus)

When you want to store numbers instead of strings:

```python
class Priority(models.IntegerChoices):
    LOW    = 1, 'Low Priority'
    MEDIUM = 2, 'Medium Priority'
    HIGH   = 3, 'High Priority'

priority = models.IntegerField(
    choices=Priority.choices,
    default=Priority.LOW
)
```

---

### 1.5 Tips and Best Practices at Model Level

- Always define `TextChoices` **inside** the model class — keeps it organised and scoped
- Use `SCREAMING_SNAKE_CASE` for constant names (`PENDING`, `IN_PROGRESS`)
- Use lowercase values for string choices (`'pending'`, `'in_progress'`) — consistent, URL-safe
- Always set a `default` for your choice field
- Never hardcode strings like `status='pending'` in your code — always use `StatusChoices.PENDING`
- Run migrations after adding or modifying choices

```python
# BAD — hardcoded strings everywhere
order.status = 'pending'
Order.objects.filter(status='shipped')

# GOOD — use the enum constant
order.status = Order.StatusChoices.PENDING
Order.objects.filter(status=Order.StatusChoices.SHIPPED)
```

---

## 2. Serializer Level

### 2.1 Basic Serializer for Choice Field

**Simple definition:**
A serializer converts your Python/Django model data into JSON (and back). For choice fields, you need to handle:
1. Sending the stored **value** (`'pending'`)
2. Sending the human-readable **label** (`'Pending'`)
3. Optionally sending **all available choices** for the frontend to render a dropdown

```python
# serializers.py

from rest_framework import serializers
from .models import Order
```

**The most basic serializer — just sends raw values:**

```python
class OrderBasicSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ['id', 'customer_name', 'total_amount', 'status']
```

**Response from this serializer:**
```json
{
  "id": 1,
  "customer_name": "Ali Khan",
  "total_amount": "1500.00",
  "status": "pending"
}
```

This is valid — but the frontend only gets `"pending"`, not `"Pending"`. Let's fix that.

---

### 2.2 Showing the Display Label using get_FOO_display

Django provides a magic method `get_FIELDNAME_display()` on every model instance that has choices. Use `SerializerMethodField` to expose it.

```python
# serializers.py

from rest_framework import serializers
from .models import Order


class OrderSerializer(serializers.ModelSerializer):

    # SerializerMethodField calls a method you define
    status_display = serializers.SerializerMethodField()

    class Meta:
        model = Order
        fields = [
            'id',
            'customer_name',
            'total_amount',
            'status',          # The raw value: 'pending'
            'status_display',  # The human label: 'Pending'
            'created_at',
        ]

    # Naming convention: get_<field_name>
    def get_status_display(self, obj):
        # obj is the Order instance
        return obj.get_status_display()   # Django built-in method
```

**Response from this serializer:**
```json
{
  "id": 1,
  "customer_name": "Ali Khan",
  "total_amount": "1500.00",
  "status": "pending",
  "status_display": "Pending",
  "created_at": "2024-01-15T10:30:00Z"
}
```

Now the frontend has both the raw value (`"pending"`) for logic and the label (`"Pending"`) for display.

---

### 2.3 Sending All Available Choices using SerializerMethodField

Sometimes the frontend needs to know **all** available options to render a dropdown. You can include them in the serializer:

```python
# serializers.py

from rest_framework import serializers
from .models import Order


class OrderDetailSerializer(serializers.ModelSerializer):

    status_display  = serializers.SerializerMethodField()
    status_choices  = serializers.SerializerMethodField()  # All available options

    class Meta:
        model = Order
        fields = [
            'id',
            'customer_name',
            'total_amount',
            'status',
            'status_display',
            'status_choices',
            'created_at',
        ]

    def get_status_display(self, obj):
        return obj.get_status_display()

    def get_status_choices(self, obj):
        # Return a list of dicts: [{"value": "pending", "label": "Pending"}, ...]
        return [
            {"value": value, "label": label}
            for value, label in Order.StatusChoices.choices
        ]
```

**Response:**
```json
{
  "id": 1,
  "customer_name": "Ali Khan",
  "total_amount": "1500.00",
  "status": "pending",
  "status_display": "Pending",
  "status_choices": [
    {"value": "pending",    "label": "Pending"},
    {"value": "processing", "label": "Processing"},
    {"value": "shipped",    "label": "Shipped"},
    {"value": "delivered",  "label": "Delivered"},
    {"value": "cancelled",  "label": "Cancelled"}
  ],
  "created_at": "2024-01-15T10:30:00Z"
}
```

---

### 2.4 Best Structure for Choice Data (Frontend-Friendly)

The `{"value": ..., "label": ...}` format is the industry standard because:
- React/Vue can directly use `value` for `<option value="...">` and `label` for display text
- It's self-documenting
- Easy to iterate over in any frontend framework

**React example (how frontend uses it):**
```jsx
// Frontend rendering choices
{order.status_choices.map(choice => (
  <option key={choice.value} value={choice.value}>
    {choice.label}
  </option>
))}
```

---

### 2.5 Advantages and Disadvantages

| Approach | Advantage | Disadvantage |
|----------|-----------|-------------|
| Raw value only | Simple, small payload | Frontend must hardcode labels |
| Value + display label | Frontend shows correct text | Slightly larger payload |
| Value + display + all choices in serializer | Everything in one call | Choices repeated on every record in a list |
| Separate meta endpoint | Choices fetched once and cached | One extra API call needed |

---

### 2.6 Tips and Best Practices at Serializer Level

- Name your display field `<field>_display` (e.g. `status_display`) — this is a Django convention
- Make `status` read-write but `status_display` and `status_choices` **read-only** — they are computed, never sent by the client
- For list endpoints (returning many objects), **do not include** `status_choices` in each record — it wastes bandwidth. Use a separate meta endpoint instead
- Always use `SerializerMethodField` for computed/derived data

```python
# Read-only fields are automatically handled by SerializerMethodField
# But for ModelSerializer fields, you can be explicit:

class OrderSerializer(serializers.ModelSerializer):
    status_display = serializers.SerializerMethodField(read_only=True)
```

---

## 3. View Level (APIView)

### 3.1 What is APIView?

**Simple definition:**
`APIView` is the base class in DRF for writing class-based views. You define methods (`get`, `post`, `put`, `patch`, `delete`) to handle HTTP requests.

**Why use APIView instead of generic views?**
- Full control over what you return
- Easy to add custom logic (like appending metadata)
- Perfect for mixing data + choices in one response

```python
# views.py

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Order
from .serializers import OrderSerializer
```

---

### 3.2 GET API — Sending Choices Inline in Response

This approach sends both the data **and** available choices in the same API response — useful for detail/edit screens.

```python
# views.py

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404

from .models import Order
from .serializers import OrderSerializer


class OrderDetailView(APIView):
    """
    GET /api/orders/<id>/  → returns order data + available choices
    PUT /api/orders/<id>/  → update an order's status
    """

    def get(self, request, pk):
        order = get_object_or_404(Order, pk=pk)
        serializer = OrderSerializer(order)

        # Build a custom response: data + choices (for edit forms)
        response_data = {
            "data": serializer.data,
            "meta": {
                "status_choices": [
                    {"value": value, "label": label}
                    for value, label in Order.StatusChoices.choices
                ]
            }
        }
        return Response(response_data, status=status.HTTP_200_OK)

    def put(self, request, pk):
        order = get_object_or_404(Order, pk=pk)
        serializer = OrderSerializer(order, data=request.data, partial=False)

        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)

        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def patch(self, request, pk):
        order = get_object_or_404(Order, pk=pk)
        serializer = OrderSerializer(order, data=request.data, partial=True)

        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)

        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

**GET Response:**
```json
{
  "data": {
    "id": 1,
    "customer_name": "Ali Khan",
    "total_amount": "1500.00",
    "status": "pending",
    "status_display": "Pending",
    "created_at": "2024-01-15T10:30:00Z"
  },
  "meta": {
    "status_choices": [
      {"value": "pending",    "label": "Pending"},
      {"value": "processing", "label": "Processing"},
      {"value": "shipped",    "label": "Shipped"},
      {"value": "delivered",  "label": "Delivered"},
      {"value": "cancelled",  "label": "Cancelled"}
    ]
  }
}
```

---

### 3.3 Separate Meta API Endpoint for Choices

This is the **preferred approach** in real projects. A dedicated endpoint returns only the choices — the frontend fetches it once and caches it.

```python
# views.py  (continued)


class OrderMetaView(APIView):
    """
    GET /api/orders/meta/
    Returns all choice options for the Order model.
    Frontend fetches this once and caches it.
    """

    def get(self, request):
        meta = {
            "status_choices": [
                {"value": value, "label": label}
                for value, label in Order.StatusChoices.choices
            ]
        }
        return Response(meta, status=status.HTTP_200_OK)


class OrderListView(APIView):
    """
    GET  /api/orders/     → list all orders (no choices here — clean)
    POST /api/orders/     → create a new order
    """

    def get(self, request):
        orders = Order.objects.all().order_by('-created_at')
        serializer = OrderSerializer(orders, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = OrderSerializer(data=request.data)

        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)

        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

**Meta endpoint response:**
```json
{
  "status_choices": [
    {"value": "pending",    "label": "Pending"},
    {"value": "processing", "label": "Processing"},
    {"value": "shipped",    "label": "Shipped"},
    {"value": "delivered",  "label": "Delivered"},
    {"value": "cancelled",  "label": "Cancelled"}
  ]
}
```

---

### 3.4 When to Use Each Approach

| Scenario | Best Approach | Why |
|----------|--------------|-----|
| Edit/Create form — single record | Include choices inline in GET response | Frontend needs data + choices together |
| List of 100+ records | Separate meta endpoint | Don't repeat choices in every record |
| Dashboard with multiple models | Shared meta endpoint | One call, all choices for all models |
| Simple admin panel | Inline in serializer | Less complexity, fewer endpoints |
| Mobile app with caching | Separate meta endpoint | Fetch once, cache locally |

---

### 3.5 URL Configuration

```python
# urls.py

from django.urls import path
from . import views

urlpatterns = [
    # Meta endpoint — always register BEFORE <pk> patterns
    path('orders/meta/',   views.OrderMetaView.as_view(),   name='order-meta'),

    # List + Create
    path('orders/',        views.OrderListView.as_view(),   name='order-list'),

    # Retrieve + Update
    path('orders/<int:pk>/', views.OrderDetailView.as_view(), name='order-detail'),
]
```

> **Important:** Register `orders/meta/` BEFORE `orders/<int:pk>/` — otherwise Django will try to match `"meta"` as a `pk` (integer) and fail.

---

### 3.6 Tips and Best Practices at View Level

- Always return consistent response structures — `{"data": ..., "meta": ...}`
- Use `status.HTTP_200_OK`, `status.HTTP_201_CREATED`, etc. — never hardcode integers
- Use `get_object_or_404` instead of `Order.objects.get(pk=pk)` to avoid writing try/except
- For list endpoints with choices, let the frontend call the meta endpoint separately
- Add `permission_classes` and `authentication_classes` in production

```python
from rest_framework.permissions import IsAuthenticated

class OrderListView(APIView):
    permission_classes = [IsAuthenticated]
    # ... rest of the view
```

---

## 4. API Usage (Postman / Hoppscotch)

### 4.1 How Frontend Fetches Choices

**Step 1:** Frontend calls the meta endpoint on app startup or page load.

```
GET http://localhost:8000/api/orders/meta/

Headers:
  Content-Type: application/json
  Authorization: Bearer <token>   (if using JWT)
```

**Response:**
```json
{
  "status_choices": [
    {"value": "pending",    "label": "Pending"},
    {"value": "processing", "label": "Processing"},
    {"value": "shipped",    "label": "Shipped"},
    {"value": "delivered",  "label": "Delivered"},
    {"value": "cancelled",  "label": "Cancelled"}
  ]
}
```

**Step 2:** Frontend stores this in state (React `useState`, Vue `ref`, etc.) and uses it to render dropdowns.

---

### 4.2 How to Send Selected Choice in POST / PUT / PATCH

**Creating a new order (POST):**
```
POST http://localhost:8000/api/orders/

Headers:
  Content-Type: application/json

Body (raw JSON):
{
  "customer_name": "Sara Ahmed",
  "total_amount": "2500.00",
  "status": "pending"
}
```

**Success Response (201 Created):**
```json
{
  "id": 2,
  "customer_name": "Sara Ahmed",
  "total_amount": "2500.00",
  "status": "pending",
  "status_display": "Pending",
  "created_at": "2024-01-16T08:00:00Z"
}
```

**Updating status (PATCH — partial update):**
```
PATCH http://localhost:8000/api/orders/2/

Headers:
  Content-Type: application/json

Body:
{
  "status": "shipped"
}
```

**Success Response (200 OK):**
```json
{
  "id": 2,
  "customer_name": "Sara Ahmed",
  "total_amount": "2500.00",
  "status": "shipped",
  "status_display": "Shipped",
  "created_at": "2024-01-16T08:00:00Z"
}
```

---

### 4.3 Common Mistakes

#### Mistake 1: Sending the label instead of the value

```json
// ❌ WRONG — sending human label
{
  "status": "Pending"
}

// ✓ CORRECT — send the stored value
{
  "status": "pending"
}
```

**Error DRF will return:**
```json
{
  "status": [
    "\"Pending\" is not a valid choice."
  ]
}
```

#### Mistake 2: Sending an invalid value

```json
// ❌ WRONG
{ "status": "done" }

// ✓ CORRECT — use only values from TextChoices
{ "status": "delivered" }
```

#### Mistake 3: Expecting display label back when you send a PATCH

After a PATCH, DRF returns the **serializer data**. If your serializer includes `status_display`, you'll get it back. If not, you only get the raw value.

#### Mistake 4: Hardcoding choices in frontend

```javascript
// ❌ WRONG — hardcoded in frontend (breaks when backend changes)
const statusOptions = ['pending', 'processing', 'shipped'];

// ✓ CORRECT — fetched from meta API
const statusOptions = await fetch('/api/orders/meta/').then(r => r.json());
```

#### Mistake 5: Forgetting Content-Type header

Always set `Content-Type: application/json` when sending JSON body in POST/PATCH/PUT.

---

### 4.4 Complete Request-Response Examples

**List all orders:**
```
GET /api/orders/
Response 200:
[
  {"id": 1, "customer_name": "Ali Khan", "status": "pending", "status_display": "Pending", ...},
  {"id": 2, "customer_name": "Sara Ahmed", "status": "shipped", "status_display": "Shipped", ...}
]
```

**Get meta (choices):**
```
GET /api/orders/meta/
Response 200:
{"status_choices": [{"value": "pending", "label": "Pending"}, ...]}
```

**Validation error response:**
```
POST /api/orders/
Body: {"customer_name": "Test", "total_amount": "100", "status": "InvalidStatus"}

Response 400:
{
  "status": ["\"InvalidStatus\" is not a valid choice."]
}
```

---

## 5. Best Practices

### 5.1 Clean Architecture Approach

**Project structure for a choice-heavy app:**

```
orders/
├── models.py          ← Define TextChoices here
├── serializers.py     ← Expose value, display, and choices
├── views.py           ← Handle meta endpoint + CRUD views
├── urls.py            ← Register meta/ before <pk>/
└── constants.py       ← (Optional) Shared choices used across models
```

**Defining shared choices in `constants.py` (advanced):**

```python
# orders/constants.py

from django.db import models


class OrderStatus(models.TextChoices):
    PENDING    = 'pending',    'Pending'
    PROCESSING = 'processing', 'Processing'
    SHIPPED    = 'shipped',    'Shipped'
    DELIVERED  = 'delivered',  'Delivered'
    CANCELLED  = 'cancelled',  'Cancelled'


class PaymentStatus(models.TextChoices):
    UNPAID  = 'unpaid',  'Unpaid'
    PAID    = 'paid',    'Paid'
    REFUNDED = 'refunded', 'Refunded'
```

```python
# orders/models.py

from .constants import OrderStatus, PaymentStatus

class Order(models.Model):
    status         = models.CharField(max_length=20, choices=OrderStatus.choices, default=OrderStatus.PENDING)
    payment_status = models.CharField(max_length=20, choices=PaymentStatus.choices, default=PaymentStatus.UNPAID)
```

---

### 5.2 Why Separate Meta APIs are Preferred

**Problem with inline choices:**
If you have a list API returning 500 orders, and each order contains all 5 status choices — you're sending the same 5 choices **500 times** in one response. That's unnecessary data transfer.

**Solution: Meta endpoint:**
```
Frontend startup:
  1. GET /api/orders/meta/   ← fetch once, cache in state
  2. GET /api/orders/        ← clean list, no repeated choices

Result: 80% smaller list response
```

**Caching meta in React:**
```javascript
// Fetch once on mount, store in context/state
useEffect(() => {
  fetch('/api/orders/meta/')
    .then(res => res.json())
    .then(data => setMeta(data));
}, []);   // Empty dependency = run once
```

**Combined meta endpoint (multiple models):**
```python
class AppMetaView(APIView):
    """
    GET /api/meta/
    Returns all choices for all models in the app.
    Frontend calls this once on app init.
    """
    def get(self, request):
        return Response({
            "order_status":   [{"value": v, "label": l} for v, l in Order.StatusChoices.choices],
            "payment_status": [{"value": v, "label": l} for v, l in PaymentStatus.choices],
            # Add more model choices here...
        })
```

---

### 5.3 How Large-Scale Projects Handle Dynamic Choices

**Scenario:** Choices that can change at runtime (e.g. stored in a database table like `Category`, `Tag`, `Country`) — these are not `TextChoices` but work similarly.

```python
# For DB-driven choices (not TextChoices)
class CategoryMetaView(APIView):
    def get(self, request):
        from products.models import Category
        choices = Category.objects.filter(is_active=True).values('id', 'name')
        return Response({
            "category_choices": [
                {"value": c['id'], "label": c['name']} for c in choices
            ]
        })
```

**Pattern used in large projects:**

1. All static choices (TextChoices) → meta endpoint (no DB query needed)
2. All dynamic choices (DB-backed) → same meta endpoint with DB query
3. Frontend caches meta with a TTL (time-to-live) — re-fetches after 5 minutes
4. Redis is used to cache meta endpoint responses at the backend level

**Backend caching with Django cache:**
```python
from django.core.cache import cache

class OrderMetaView(APIView):
    def get(self, request):
        cached = cache.get('order_meta')
        if cached:
            return Response(cached)

        meta = {
            "status_choices": [
                {"value": v, "label": l}
                for v, l in Order.StatusChoices.choices
            ]
        }
        cache.set('order_meta', meta, timeout=3600)   # Cache for 1 hour
        return Response(meta)
```

---

## 6. Complete Working Project Example

This is a minimal but complete Django REST Framework project demonstrating everything covered above.

### models.py

```python
from django.db import models


class Order(models.Model):

    class StatusChoices(models.TextChoices):
        PENDING    = 'pending',    'Pending'
        PROCESSING = 'processing', 'Processing'
        SHIPPED    = 'shipped',    'Shipped'
        DELIVERED  = 'delivered',  'Delivered'
        CANCELLED  = 'cancelled',  'Cancelled'

    class PaymentChoices(models.TextChoices):
        UNPAID   = 'unpaid',   'Unpaid'
        PAID     = 'paid',     'Paid'
        REFUNDED = 'refunded', 'Refunded'

    customer_name  = models.CharField(max_length=200)
    total_amount   = models.DecimalField(max_digits=10, decimal_places=2)
    status         = models.CharField(
                        max_length=20,
                        choices=StatusChoices.choices,
                        default=StatusChoices.PENDING
                     )
    payment_status = models.CharField(
                        max_length=20,
                        choices=PaymentChoices.choices,
                        default=PaymentChoices.UNPAID
                     )
    created_at     = models.DateTimeField(auto_now_add=True)
    updated_at     = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f"Order #{self.id} — {self.customer_name} ({self.get_status_display()})"
```

### serializers.py

```python
from rest_framework import serializers
from .models import Order


class OrderSerializer(serializers.ModelSerializer):

    # Read-only display fields
    status_display         = serializers.SerializerMethodField()
    payment_status_display = serializers.SerializerMethodField()

    class Meta:
        model  = Order
        fields = [
            'id',
            'customer_name',
            'total_amount',
            'status',
            'status_display',
            'payment_status',
            'payment_status_display',
            'created_at',
            'updated_at',
        ]
        read_only_fields = ['id', 'created_at', 'updated_at']

    def get_status_display(self, obj):
        return obj.get_status_display()

    def get_payment_status_display(self, obj):
        return obj.get_payment_status_display()
```

### views.py

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404

from .models import Order
from .serializers import OrderSerializer


class OrderMetaView(APIView):
    """GET /api/orders/meta/ — all choices for the Order model"""

    def get(self, request):
        return Response({
            "status_choices": [
                {"value": v, "label": l}
                for v, l in Order.StatusChoices.choices
            ],
            "payment_choices": [
                {"value": v, "label": l}
                for v, l in Order.PaymentChoices.choices
            ],
        }, status=status.HTTP_200_OK)


class OrderListView(APIView):
    """
    GET  /api/orders/  → list all orders
    POST /api/orders/  → create a new order
    """

    def get(self, request):
        orders     = Order.objects.all().order_by('-created_at')
        serializer = OrderSerializer(orders, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = OrderSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class OrderDetailView(APIView):
    """
    GET    /api/orders/<pk>/  → retrieve a single order
    PUT    /api/orders/<pk>/  → full update
    PATCH  /api/orders/<pk>/  → partial update
    DELETE /api/orders/<pk>/  → delete
    """

    def get(self, request, pk):
        order      = get_object_or_404(Order, pk=pk)
        serializer = OrderSerializer(order)
        return Response({
            "data": serializer.data,
            "meta": {
                "status_choices": [
                    {"value": v, "label": l}
                    for v, l in Order.StatusChoices.choices
                ],
            }
        }, status=status.HTTP_200_OK)

    def put(self, request, pk):
        order      = get_object_or_404(Order, pk=pk)
        serializer = OrderSerializer(order, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def patch(self, request, pk):
        order      = get_object_or_404(Order, pk=pk)
        serializer = OrderSerializer(order, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        order = get_object_or_404(Order, pk=pk)
        order.delete()
        return Response({"message": "Order deleted."}, status=status.HTTP_204_NO_CONTENT)
```

### urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    # Meta MUST come before <int:pk>
    path('orders/meta/',     views.OrderMetaView.as_view(),   name='order-meta'),
    path('orders/',          views.OrderListView.as_view(),   name='order-list'),
    path('orders/<int:pk>/', views.OrderDetailView.as_view(), name='order-detail'),
]
```

### project/urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('orders.urls')),
]
```

### settings.py (minimum required)

```python
INSTALLED_APPS = [
    # Django defaults ...
    'rest_framework',
    'orders',
]

REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
    ],
}
```

---

## Quick Reference Cheatsheet

```
TextChoices structure:
  CONSTANT = 'db_value', 'Human Label'
  
Accessing in Python:
  Order.StatusChoices.PENDING          → enum member
  Order.StatusChoices.PENDING.value    → 'pending'
  Order.StatusChoices.PENDING.label    → 'Pending'
  Order.StatusChoices.choices          → [('pending','Pending'), ...]
  
On model instance:
  order.status                         → 'pending'
  order.get_status_display()           → 'Pending'
  
In serializer:
  status_display = SerializerMethodField()
  def get_status_display(self, obj):
      return obj.get_status_display()

Frontend sends:    "status": "pending"    ← always the VALUE
Frontend receives: "status": "pending"    ← value for logic
                   "status_display": "Pending"  ← label for UI

Meta endpoint:
  GET /api/orders/meta/
  → {"status_choices": [{"value": "pending", "label": "Pending"}, ...]}

URL order:   meta/ before <int:pk>/
```
