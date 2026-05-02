# Django REST Framework — Complete Notes
> Senior Developer → Intern Teaching Style | Production-Ready Patterns

---

## Table of Contents

1. [Models](#1-models)
   - [1.1 How to Design Models](#11-how-to-design-models)
   - [1.2 Field Types and Best Practices](#12-field-types-and-best-practices)
   - [1.3 Choices Fields using TextChoices](#13-choices-fields-using-textchoices)
   - [1.4 Model Methods — `__str__`, `clean`](#14-model-methods----__str__-clean)
   - [1.5 When to Use Validators](#15-when-to-use-validators)
2. [Serializers](#2-serializers)
   - [2.1 ModelSerializer vs Serializer](#21-modelserializer-vs-serializer)
   - [2.2 Meta Class Usage](#22-meta-class-usage)
   - [2.3 read_only_fields and required Fields](#23-read_only_fields-and-required-fields)
   - [2.4 Field-level and Object-level Validation](#24-field-level-and-object-level-validation)
   - [2.5 How to Expose Choice Fields for Frontend](#25-how-to-expose-choice-fields-for-frontend)
   - [2.6 Custom Fields and Methods](#26-custom-fields-and-methods)
3. [Views](#3-views)
   - [3.1 Generic Views vs APIView vs ViewSets](#31-generic-views-vs-apiview-vs-viewsets)
   - [3.2 Proper Structure](#32-proper-structure)
   - [3.3 perform_create and get_queryset](#33-perform_create-and-get_queryset)
   - [3.4 Where to Write Business Logic](#34-where-to-write-business-logic)
4. [Response Structure](#4-response-structure)
   - [4.1 Standard API Response Format](#41-standard-api-response-format)
   - [4.2 Error Handling Best Practices](#42-error-handling-best-practices)
5. [URLs](#5-urls)
   - [5.1 RESTful URL Design](#51-restful-url-design)
   - [5.2 Naming Conventions](#52-naming-conventions)
   - [5.3 Versioning Basics](#53-versioning-basics)
6. [Filtering & Search](#6-filtering--search)
   - [6.1 DjangoFilterBackend](#61-djangofilterbackend)
   - [6.2 SearchFilter](#62-searchfilter)
   - [6.3 Query Params Usage](#63-query-params-usage)
7. [Admin Panel](#7-admin-panel)
   - [7.1 list_display, search_fields, filters](#71-list_display-search_fields-filters)
   - [7.2 Inline Models](#72-inline-models)
   - [7.3 Best Practices](#73-best-practices)
8. [Project Structure](#8-project-structure)
   - [8.1 Where to Use services, utils](#81-where-to-use-services-utils)
   - [8.2 Keeping Views Clean](#82-keeping-views-clean)
9. [Middleware — Basic Overview](#9-middleware--basic-overview)
10. [API Documentation](#10-api-documentation)
    - [10.1 Postman / Hoppscotch Documentation](#101-postman--hoppscotch-documentation)
    - [10.2 Defining API Flow](#102-defining-api-flow)
    - [10.3 Documenting Filters, Request Body, and Responses](#103-documenting-filters-request-body-and-responses)

---

# 1. Models

## 1.1 How to Design Models

### Simple Definition
A **model** is a Python class that represents a database table. Each attribute maps to a column.

### Why It Is Used
Models define your data structure. Everything in your API revolves around models — serializers read them, views query them, and the admin displays them.

### Design Rules (Production Standard)
- One model = one real-world concept (User, Order, Product)
- Never duplicate data — use ForeignKey/OneToOne
- Always add `created_at` and `updated_at` timestamps
- Use abstract base models for shared fields
- Keep models in `models/` folder for large apps (split by domain)

### Base Model Pattern (Use in Every Project)

```python
# core/models.py
import uuid
from django.db import models

class BaseModel(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    is_active = models.BooleanField(default=True)

    class Meta:
        abstract = True  # ← This is key. No DB table created for this.
```

```python
# products/models.py
from core.models import BaseModel

class Product(BaseModel):
    name = models.CharField(max_length=255)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.PositiveIntegerField(default=0)

    class Meta:
        db_table = "products"
        ordering = ["-created_at"]
```

---

## 1.2 Field Types and Best Practices

### Common Field Types

| Field | Use Case |
|---|---|
| `CharField` | Short text (name, title) — always set `max_length` |
| `TextField` | Long text (description, bio) |
| `IntegerField` | Whole numbers |
| `DecimalField` | Money, precise decimals — use instead of FloatField |
| `BooleanField` | True/False flags |
| `DateTimeField` | Timestamps |
| `DateField` | Dates only |
| `EmailField` | Validated email |
| `URLField` | Validated URL |
| `UUIDField` | UUID primary keys |
| `JSONField` | Storing arbitrary JSON |
| `ImageField` / `FileField` | File uploads |
| `ForeignKey` | Many-to-one relationship |
| `ManyToManyField` | Many-to-many relationship |
| `OneToOneField` | One-to-one relationship |

### Best Practices

```python
class UserProfile(BaseModel):
    # ✅ Use null=True, blank=True only on optional fields
    bio = models.TextField(null=True, blank=True)

    # ✅ Use blank=True for optional CharField (not null=True — empty string is fine)
    phone = models.CharField(max_length=20, blank=True, default="")

    # ✅ Use DecimalField for money — never FloatField
    wallet_balance = models.DecimalField(max_digits=12, decimal_places=2, default=0.00)

    # ✅ Use related_name for reverse lookups
    user = models.OneToOneField(
        "auth.User",
        on_delete=models.CASCADE,
        related_name="profile"
    )

    # ✅ on_delete choices:
    # CASCADE   → delete child when parent deleted
    # SET_NULL  → set FK to null (requires null=True)
    # PROTECT   → prevent parent deletion if children exist
    # SET_DEFAULT → set FK to default value
```

### Tips
- Never use `FloatField` for currency — floating point errors will ruin calculations
- Use `db_index=True` on fields you filter/search by frequently
- Use `unique=True` on fields that must be unique (email, slug)
- Use `default=` to avoid migration headaches on existing data

---

## 1.3 Choices Fields using TextChoices

### Simple Definition
`TextChoices` is a way to define a fixed set of allowed values for a field — like an enum.

### Why It Is Used
Prevents invalid values from entering the DB. Makes code readable. Provides a single source of truth.

```python
# orders/models.py
from django.db import models

class Order(models.Model):

    class Status(models.TextChoices):
        PENDING   = "pending",   "Pending"
        CONFIRMED = "confirmed", "Confirmed"
        SHIPPED   = "shipped",   "Shipped"
        DELIVERED = "delivered", "Delivered"
        CANCELLED = "cancelled", "Cancelled"

    class PaymentMethod(models.TextChoices):
        CARD   = "card",   "Credit/Debit Card"
        UPI    = "upi",    "UPI"
        COD    = "cod",    "Cash on Delivery"
        WALLET = "wallet", "Wallet"

    status = models.CharField(
        max_length=20,
        choices=Status.choices,
        default=Status.PENDING
    )
    payment_method = models.CharField(
        max_length=20,
        choices=PaymentMethod.choices
    )
```

### Using Choices in Code

```python
# Check a value
if order.status == Order.Status.PENDING:
    send_confirmation_email(order)

# Query by choice
pending_orders = Order.objects.filter(status=Order.Status.PENDING)

# Get human-readable label
order.get_status_display()  # → "Pending"
```

### Tips
- Always use `TextChoices` (or `IntegerChoices`) — never raw strings like `"pending"` in conditions
- The first value is stored in DB, second is the human-readable label
- Access all choices: `Order.Status.choices` → list of `(value, label)` tuples

---

## 1.4 Model Methods — `__str__`, `clean`

### `__str__` Method

```python
class Product(BaseModel):
    name = models.CharField(max_length=255)
    sku = models.CharField(max_length=50)

    def __str__(self):
        # Used in admin panel, shell, and debugging
        return f"{self.name} ({self.sku})"
```

### `clean` Method — Model-level Validation

```python
from django.core.exceptions import ValidationError
from django.utils import timezone

class Event(BaseModel):
    title = models.CharField(max_length=255)
    start_date = models.DateTimeField()
    end_date = models.DateTimeField()

    def clean(self):
        # Cross-field validation goes here
        if self.end_date <= self.start_date:
            raise ValidationError("End date must be after start date.")

        if self.start_date < timezone.now():
            raise ValidationError("Start date cannot be in the past.")

    def save(self, *args, **kwargs):
        self.full_clean()  # ← Triggers clean() automatically on save
        super().save(*args, **kwargs)
```

### Custom Model Methods (Business Logic)

```python
class Order(BaseModel):
    total_amount = models.DecimalField(max_digits=10, decimal_places=2)
    discount = models.DecimalField(max_digits=5, decimal_places=2, default=0)

    @property
    def final_amount(self):
        return self.total_amount - self.discount

    def cancel(self):
        if self.status == self.Status.DELIVERED:
            raise ValueError("Cannot cancel a delivered order.")
        self.status = self.Status.CANCELLED
        self.save(update_fields=["status", "updated_at"])
```

### Tips
- `__str__` should always return something meaningful
- `clean()` is for cross-field logic — use field validators for single-field logic
- Call `self.full_clean()` inside `save()` to enforce validation even from code (not just forms/serializers)
- `update_fields` in `save()` only updates specified columns → better performance

---

## 1.5 When to Use Validators

### Simple Definition
Validators are functions that check a single field value and raise `ValidationError` if it's wrong.

### Types

```python
# 1. Django built-in validators
from django.core.validators import MinValueValidator, MaxValueValidator, RegexValidator

class Product(BaseModel):
    price = models.DecimalField(
        max_digits=10,
        decimal_places=2,
        validators=[MinValueValidator(0.01)]  # Price must be > 0
    )
    rating = models.IntegerField(
        validators=[MinValueValidator(1), MaxValueValidator(5)]
    )
    phone = models.CharField(
        max_length=15,
        validators=[RegexValidator(r'^\+?1?\d{9,15}$', 'Enter a valid phone number.')]
    )
```

```python
# 2. Custom validator function
from django.core.exceptions import ValidationError

def validate_not_future_date(value):
    from django.utils import timezone
    if value > timezone.now().date():
        raise ValidationError("Date cannot be in the future.")

class Certificate(BaseModel):
    issue_date = models.DateField(validators=[validate_not_future_date])
```

### When to Use What

| Validation Type | Use When |
|---|---|
| Field `validators=[]` | Single field, reusable rule |
| Model `clean()` | Cross-field logic |
| Serializer `validate_<field>` | API-specific rules |
| Serializer `validate()` | Cross-field at API level |

---

# 2. Serializers

## 2.1 ModelSerializer vs Serializer

### Simple Definition
A **serializer** converts complex data (Django model instances) to Python dicts/JSON, and vice versa (JSON → validated Python data → model).

### `ModelSerializer` — Use 90% of the time

```python
# products/serializers.py
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ["id", "name", "price", "stock", "created_at"]
```

`ModelSerializer` automatically:
- Creates fields from the model
- Creates `create()` and `update()` methods
- Handles validation based on model field definitions

### `Serializer` — Use for custom/non-model data

```python
# Use when no model is involved (login, OTP, custom reports)
class LoginSerializer(serializers.Serializer):
    email = serializers.EmailField()
    password = serializers.CharField(write_only=True, min_length=8)
```

### Comparison

| Feature | `ModelSerializer` | `Serializer` |
|---|---|---|
| Auto-generates fields | ✅ Yes | ❌ No |
| Tied to a model | ✅ Yes | ❌ No |
| `create()` / `update()` | ✅ Auto | ❌ Manual |
| Best for | CRUD APIs | Login, OTP, Reports |

---

## 2.2 Meta Class Usage

```python
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product

        # Option 1: Explicit fields (RECOMMENDED — always be explicit)
        fields = ["id", "name", "price", "stock", "status", "created_at"]

        # Option 2: All fields (avoid in production — exposes everything)
        # fields = "__all__"

        # Option 3: Exclude specific fields
        # exclude = ["internal_notes"]

        # Extra kwargs to modify field behavior
        extra_kwargs = {
            "price": {"min_value": 0},
            "name": {"required": True},
            "stock": {"default": 0},
        }
```

### Tips
- Always use `fields = [...]` explicitly — never `"__all__"` in production
- `extra_kwargs` is cleaner than redefining fields just to change one property

---

## 2.3 read_only_fields and required Fields

```python
class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = [
            "id", "status", "total_amount", "created_at",
            "payment_method", "user"
        ]
        read_only_fields = [
            "id",           # Always read-only (auto-generated)
            "created_at",   # Always read-only (auto-set)
            "status",       # Set by system, not user
            "user",         # Set from request, not user input
        ]
        extra_kwargs = {
            "payment_method": {"required": True},
            "total_amount": {"required": True},
        }
```

### Pattern: Write-only fields

```python
class UserSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, min_length=8)

    class Meta:
        model = User
        fields = ["id", "email", "password", "created_at"]
        read_only_fields = ["id", "created_at"]
```

### Summary

| Attribute | Purpose |
|---|---|
| `read_only_fields` | Fields shown in response but not accepted in request |
| `write_only=True` | Fields accepted in request but not shown in response |
| `required=True/False` | Whether field is mandatory in request |

---

## 2.4 Field-level and Object-level Validation

### Field-level Validation (`validate_<fieldname>`)

```python
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ["name", "price", "stock"]

    def validate_price(self, value):
        # value is already the cleaned Python value
        if value <= 0:
            raise serializers.ValidationError("Price must be greater than zero.")
        return value

    def validate_name(self, value):
        # Normalize name to title case
        return value.strip().title()
```

### Object-level Validation (`validate`)

```python
class EventSerializer(serializers.ModelSerializer):
    class Meta:
        model = Event
        fields = ["title", "start_date", "end_date"]

    def validate(self, data):
        # data is a dict with all field values
        if data["end_date"] <= data["start_date"]:
            raise serializers.ValidationError({
                "end_date": "End date must be after start date."
            })
        return data
```

### Validation Flow

```
Request Body
    ↓
Field-type validation (is it a valid integer/string/etc?)
    ↓
validate_<field>() for each field
    ↓
validate() object-level
    ↓
serializer.is_valid() → True/False
    ↓
serializer.save() → calls create() or update()
```

---

## 2.5 How to Expose Choice Fields for Frontend

### Problem
Frontend needs both the stored value (`"pending"`) AND the human label (`"Pending"`) to build dropdowns.

### Solution: SerializerMethodField

```python
class OrderSerializer(serializers.ModelSerializer):
    status_display = serializers.SerializerMethodField()
    payment_method_display = serializers.SerializerMethodField()

    class Meta:
        model = Order
        fields = [
            "id", "status", "status_display",
            "payment_method", "payment_method_display"
        ]

    def get_status_display(self, obj):
        return obj.get_status_display()

    def get_payment_method_display(self, obj):
        return obj.get_payment_method_display()
```

### Response Structure

```json
{
  "id": "uuid-here",
  "status": "pending",
  "status_display": "Pending",
  "payment_method": "upi",
  "payment_method_display": "UPI"
}
```

### Bonus: API to get all choices (for dropdowns)

```python
# views.py
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Order

@api_view(["GET"])
def order_choices(request):
    return Response({
        "status": [{"value": v, "label": l} for v, l in Order.Status.choices],
        "payment_method": [{"value": v, "label": l} for v, l in Order.PaymentMethod.choices],
    })
```

---

## 2.6 Custom Fields and Methods

### SerializerMethodField

```python
class ProductSerializer(serializers.ModelSerializer):
    is_in_stock = serializers.SerializerMethodField()
    full_image_url = serializers.SerializerMethodField()

    class Meta:
        model = Product
        fields = ["id", "name", "price", "stock", "is_in_stock", "full_image_url"]

    def get_is_in_stock(self, obj):
        return obj.stock > 0

    def get_full_image_url(self, obj):
        request = self.context.get("request")
        if obj.image and request:
            return request.build_absolute_uri(obj.image.url)
        return None
```

### Nested Serializers

```python
class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ["id", "name"]

class ProductSerializer(serializers.ModelSerializer):
    category = CategorySerializer(read_only=True)          # Read: full object
    category_id = serializers.PrimaryKeyRelatedField(      # Write: just ID
        queryset=Category.objects.all(),
        source="category",
        write_only=True
    )

    class Meta:
        model = Product
        fields = ["id", "name", "price", "category", "category_id"]
```

### Custom `create` and `update`

```python
class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ["id", "items", "payment_method"]

    def create(self, validated_data):
        items_data = validated_data.pop("items", [])
        order = Order.objects.create(**validated_data)
        for item in items_data:
            OrderItem.objects.create(order=order, **item)
        return order
```

---

# 3. Views

## 3.1 Generic Views vs APIView vs ViewSets

### Simple Definition

| Type | What It Is | When to Use |
|---|---|---|
| `APIView` | Raw class-based view | Custom logic, non-CRUD endpoints |
| Generic Views | Pre-built CRUD views | Standard CRUD, fast development |
| `ViewSet` | Combines multiple views | Full CRUD with router |
| `ModelViewSet` | All CRUD in one class | Most common in production |

### `APIView` — Full Manual Control

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class ProductListView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        products = Product.objects.filter(is_active=True)
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ProductSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

### Generic Views — Less Code, Same Result

```python
from rest_framework import generics

class ProductListCreateView(generics.ListCreateAPIView):
    queryset = Product.objects.filter(is_active=True)
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]

class ProductDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]
```

### `ModelViewSet` — Most Common in Production

```python
from rest_framework.viewsets import ModelViewSet
from rest_framework.decorators import action

class ProductViewSet(ModelViewSet):
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        return Product.objects.filter(is_active=True)

    # Custom action: POST /products/{id}/deactivate/
    @action(detail=True, methods=["post"])
    def deactivate(self, request, pk=None):
        product = self.get_object()
        product.is_active = False
        product.save(update_fields=["is_active"])
        return Response({"message": "Product deactivated."})
```

---

## 3.2 Proper Structure

### The Golden Order Inside a View

```python
class OrderViewSet(ModelViewSet):
    # 1. Permission classes first
    permission_classes = [IsAuthenticated]

    # 2. Serializer class
    serializer_class = OrderSerializer

    # 3. Queryset (use get_queryset for dynamic filtering)
    def get_queryset(self):
        return Order.objects.filter(
            user=self.request.user,
            is_active=True
        ).select_related("user").prefetch_related("items")

    # 4. Override create/update for business logic
    def perform_create(self, serializer):
        serializer.save(user=self.request.user)

    # 5. Custom responses when needed
    def destroy(self, request, *args, **kwargs):
        instance = self.get_object()
        if instance.status == Order.Status.DELIVERED:
            return Response(
                {"error": "Cannot delete a delivered order."},
                status=status.HTTP_400_BAD_REQUEST
            )
        return super().destroy(request, *args, **kwargs)
```

### Different Serializers for Different Actions

```python
class ProductViewSet(ModelViewSet):
    def get_serializer_class(self):
        if self.action == "list":
            return ProductListSerializer      # Lightweight — for list
        elif self.action == "create":
            return ProductCreateSerializer    # Write-only fields
        return ProductDetailSerializer        # Full detail
```

---

## 3.3 perform_create and get_queryset

### `perform_create` — Inject Data on Create

```python
class PostViewSet(ModelViewSet):
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticated]

    def perform_create(self, serializer):
        # Automatically attach the logged-in user
        serializer.save(
            author=self.request.user,
            ip_address=self.request.META.get("REMOTE_ADDR")
        )
```

### `get_queryset` — Dynamic Filtering

```python
class OrderViewSet(ModelViewSet):
    serializer_class = OrderSerializer

    def get_queryset(self):
        qs = Order.objects.all()

        # Filter by logged-in user (non-admin)
        if not self.request.user.is_staff:
            qs = qs.filter(user=self.request.user)

        # Filter by status from query param
        status_param = self.request.query_params.get("status")
        if status_param:
            qs = qs.filter(status=status_param)

        return qs.select_related("user").order_by("-created_at")
```

---

## 3.4 Where to Write Business Logic

### The Rule: Views are thin, Services are fat

```
View  →  calls Service  →  Service does the work  →  View returns Response
```

```python
# orders/services.py
from django.db import transaction

class OrderService:
    @staticmethod
    @transaction.atomic
    def place_order(user, cart_items, payment_method):
        """All business logic lives here."""
        total = sum(item.price * item.quantity for item in cart_items)

        order = Order.objects.create(
            user=user,
            total_amount=total,
            payment_method=payment_method
        )

        for item in cart_items:
            OrderItem.objects.create(
                order=order,
                product=item.product,
                quantity=item.quantity,
                price=item.price
            )
            # Decrease stock
            item.product.stock -= item.quantity
            item.product.save(update_fields=["stock"])

        return order
```

```python
# orders/views.py
class PlaceOrderView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        serializer = PlaceOrderSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        try:
            order = OrderService.place_order(
                user=request.user,
                cart_items=serializer.validated_data["items"],
                payment_method=serializer.validated_data["payment_method"]
            )
        except ValueError as e:
            return Response({"error": str(e)}, status=400)

        return Response(OrderSerializer(order).data, status=201)
```

---

# 4. Response Structure

## 4.1 Standard API Response Format

### Define a consistent response wrapper

```python
# core/utils.py
from rest_framework.response import Response

def success_response(data=None, message="Success", status_code=200):
    return Response({
        "success": True,
        "message": message,
        "data": data
    }, status=status_code)

def error_response(message="Error", errors=None, status_code=400):
    return Response({
        "success": False,
        "message": message,
        "errors": errors or {}
    }, status=status_code)
```

### Usage in Views

```python
from core.utils import success_response, error_response

class ProductView(APIView):
    def get(self, request, pk):
        try:
            product = Product.objects.get(pk=pk, is_active=True)
        except Product.DoesNotExist:
            return error_response("Product not found.", status_code=404)

        serializer = ProductSerializer(product)
        return success_response(data=serializer.data)
```

### Standard Success Response

```json
{
  "success": true,
  "message": "Product fetched successfully.",
  "data": {
    "id": "uuid",
    "name": "Nike Air Max",
    "price": "5999.00"
  }
}
```

### Standard Error Response

```json
{
  "success": false,
  "message": "Validation failed.",
  "errors": {
    "price": ["Price must be greater than zero."],
    "name": ["This field is required."]
  }
}
```

### Paginated Response

```json
{
  "success": true,
  "message": "Products fetched.",
  "data": {
    "count": 100,
    "next": "http://api.example.com/products/?page=3",
    "previous": "http://api.example.com/products/?page=1",
    "results": [...]
  }
}
```

---

## 4.2 Error Handling Best Practices

### Global Exception Handler

```python
# core/exceptions.py
from rest_framework.views import exception_handler
from rest_framework.response import Response

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)

    if response is not None:
        return Response({
            "success": False,
            "message": "An error occurred.",
            "errors": response.data
        }, status=response.status_code)

    # Unhandled exceptions → 500
    return Response({
        "success": False,
        "message": "Internal server error.",
        "errors": {}
    }, status=500)
```

```python
# settings.py
REST_FRAMEWORK = {
    "EXCEPTION_HANDLER": "core.exceptions.custom_exception_handler",
}
```

### Use `raise_exception=True`

```python
# Instead of manual if/else:
serializer = ProductSerializer(data=request.data)
serializer.is_valid(raise_exception=True)  # ← Auto-returns 400 with errors
serializer.save()
```

---

# 5. URLs

## 5.1 RESTful URL Design

### Rules

| Action | Method | URL |
|---|---|---|
| List all | `GET` | `/api/v1/products/` |
| Create | `POST` | `/api/v1/products/` |
| Retrieve one | `GET` | `/api/v1/products/{id}/` |
| Full update | `PUT` | `/api/v1/products/{id}/` |
| Partial update | `PATCH` | `/api/v1/products/{id}/` |
| Delete | `DELETE` | `/api/v1/products/{id}/` |
| Custom action | `POST` | `/api/v1/products/{id}/deactivate/` |
| Nested resource | `GET` | `/api/v1/orders/{id}/items/` |

### Project URLs

```python
# config/urls.py
from django.urls import path, include

urlpatterns = [
    path("api/v1/", include("config.api_v1_urls")),
]
```

```python
# config/api_v1_urls.py
from django.urls import path, include

urlpatterns = [
    path("auth/",     include("apps.authentication.urls")),
    path("products/", include("apps.products.urls")),
    path("orders/",   include("apps.orders.urls")),
]
```

---

## 5.2 Naming Conventions

### With Router (ViewSets)

```python
# products/urls.py
from rest_framework.routers import DefaultRouter
from .views import ProductViewSet

router = DefaultRouter()
router.register("", ProductViewSet, basename="product")

urlpatterns = router.urls

# Auto-generates:
# products/          → product-list
# products/{id}/     → product-detail
# products/{id}/deactivate/ → product-deactivate (custom action)
```

### With Generic Views

```python
# orders/urls.py
from django.urls import path
from .views import OrderListCreateView, OrderDetailView, OrderChoicesView

urlpatterns = [
    path("",           OrderListCreateView.as_view(), name="order-list"),
    path("<uuid:pk>/", OrderDetailView.as_view(),     name="order-detail"),
    path("choices/",   OrderChoicesView.as_view(),    name="order-choices"),
]
```

---

## 5.3 Versioning Basics

### URL-based Versioning (Most Common)

```
/api/v1/products/   ← stable
/api/v2/products/   ← new version
```

```python
# config/urls.py
urlpatterns = [
    path("api/v1/", include("config.api_v1_urls")),
    path("api/v2/", include("config.api_v2_urls")),
]
```

### DRF Built-in Versioning

```python
# settings.py
REST_FRAMEWORK = {
    "DEFAULT_VERSIONING_CLASS": "rest_framework.versioning.URLPathVersioning",
    "DEFAULT_VERSION": "v1",
    "ALLOWED_VERSIONS": ["v1", "v2"],
}
```

```python
# In view
class ProductViewSet(ModelViewSet):
    def get_serializer_class(self):
        if self.request.version == "v2":
            return ProductV2Serializer
        return ProductSerializer
```

---

# 6. Filtering & Search

## 6.1 DjangoFilterBackend

### Setup

```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS = ["django_filters", ...]

REST_FRAMEWORK = {
    "DEFAULT_FILTER_BACKENDS": [
        "django_filters.rest_framework.DjangoFilterBackend",
        "rest_framework.filters.SearchFilter",
        "rest_framework.filters.OrderingFilter",
    ]
}
```

### Basic Usage

```python
# products/views.py
from django_filters.rest_framework import DjangoFilterBackend

class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ["category", "status", "is_active"]

# Usage:
# GET /api/v1/products/?category=electronics&status=active
```

### Advanced FilterSet

```python
# products/filters.py
import django_filters
from .models import Product

class ProductFilter(django_filters.FilterSet):
    min_price = django_filters.NumberFilter(field_name="price", lookup_expr="gte")
    max_price = django_filters.NumberFilter(field_name="price", lookup_expr="lte")
    name      = django_filters.CharFilter(field_name="name",  lookup_expr="icontains")
    created_after = django_filters.DateFilter(field_name="created_at", lookup_expr="date__gte")

    class Meta:
        model = Product
        fields = ["category", "status", "min_price", "max_price", "name"]
```

```python
# products/views.py
class ProductViewSet(ModelViewSet):
    filterset_class = ProductFilter

# Usage:
# GET /api/v1/products/?min_price=100&max_price=5000&name=nike
```

---

## 6.2 SearchFilter

```python
from rest_framework.filters import SearchFilter, OrderingFilter

class ProductViewSet(ModelViewSet):
    filter_backends = [DjangoFilterBackend, SearchFilter, OrderingFilter]
    filterset_class = ProductFilter

    # SearchFilter: searches across these fields
    search_fields = ["name", "description", "category__name", "=sku"]
    # ^ prefix meanings:
    # (none)  → icontains (default)
    # =       → exact match
    # ^       → startswith
    # @       → full-text search (PostgreSQL only)

    # OrderingFilter
    ordering_fields = ["price", "created_at", "name"]
    ordering = ["-created_at"]  # default ordering

# Usage:
# GET /api/v1/products/?search=nike&ordering=-price
```

---

## 6.3 Query Params Usage

```python
class OrderViewSet(ModelViewSet):
    def get_queryset(self):
        qs = Order.objects.filter(user=self.request.user)

        # Manual query param handling
        status = self.request.query_params.get("status")
        date_from = self.request.query_params.get("date_from")
        date_to = self.request.query_params.get("date_to")

        if status:
            qs = qs.filter(status=status)
        if date_from:
            qs = qs.filter(created_at__date__gte=date_from)
        if date_to:
            qs = qs.filter(created_at__date__lte=date_to)

        return qs

# Usage:
# GET /api/v1/orders/?status=pending&date_from=2024-01-01&date_to=2024-12-31
```

---

# 7. Admin Panel

## 7.1 list_display, search_fields, filters

```python
# products/admin.py
from django.contrib import admin
from .models import Product, Category

@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    # Columns shown in list view
    list_display = ["name", "sku", "price", "stock", "status", "is_active", "created_at"]

    # Clickable column (links to detail)
    list_display_links = ["name", "sku"]

    # Editable directly from list view
    list_editable = ["price", "stock", "is_active"]

    # Right-side filter panel
    list_filter = ["status", "is_active", "category", "created_at"]

    # Top search bar
    search_fields = ["name", "sku", "description"]

    # Default ordering
    ordering = ["-created_at"]

    # Read-only in detail view
    readonly_fields = ["id", "created_at", "updated_at"]

    # Pagination
    list_per_page = 25

    # Fields in detail form
    fieldsets = (
        ("Basic Info", {"fields": ("name", "sku", "description")}),
        ("Pricing & Stock", {"fields": ("price", "stock")}),
        ("Status", {"fields": ("status", "is_active")}),
        ("Timestamps", {"fields": ("id", "created_at", "updated_at"), "classes": ("collapse",)}),
    )
```

---

## 7.2 Inline Models

```python
# orders/admin.py
from django.contrib import admin
from .models import Order, OrderItem

class OrderItemInline(admin.TabularInline):  # or StackedInline
    model = OrderItem
    extra = 0          # no empty extra forms
    readonly_fields = ["product", "quantity", "price"]
    can_delete = False

@admin.register(Order)
class OrderAdmin(admin.ModelAdmin):
    list_display = ["id", "user", "status", "total_amount", "created_at"]
    list_filter = ["status", "payment_method"]
    search_fields = ["user__email", "id"]
    readonly_fields = ["id", "created_at", "updated_at"]
    inlines = [OrderItemInline]  # ← Shows order items inside order detail
```

---

## 7.3 Best Practices

```python
@admin.register(Order)
class OrderAdmin(admin.ModelAdmin):
    # Custom computed column
    def order_total_display(self, obj):
        return f"₹{obj.total_amount:,.2f}"
    order_total_display.short_description = "Total Amount"
    order_total_display.admin_order_field = "total_amount"

    # Custom action
    @admin.action(description="Mark selected as Confirmed")
    def mark_confirmed(self, request, queryset):
        updated = queryset.update(status=Order.Status.CONFIRMED)
        self.message_user(request, f"{updated} orders marked as confirmed.")

    actions = ["mark_confirmed"]
    list_display = ["id", "user", "order_total_display", "status"]
```

---

# 8. Project Structure

## 8.1 Where to Use services, utils

### Recommended Project Layout

```
project_root/
├── config/
│   ├── settings/
│   │   ├── base.py
│   │   ├── development.py
│   │   └── production.py
│   ├── urls.py
│   └── wsgi.py
│
├── apps/
│   ├── authentication/
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   ├── services.py       ← Business logic
│   │   ├── admin.py
│   │   └── tests.py
│   │
│   ├── products/
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   ├── filters.py        ← FilterSets
│   │   ├── services.py
│   │   ├── admin.py
│   │   └── tests.py
│   │
│   └── orders/
│       └── ...
│
├── core/
│   ├── models.py             ← BaseModel
│   ├── utils.py              ← Response helpers
│   ├── exceptions.py         ← Custom exception handler
│   ├── permissions.py        ← Custom permissions
│   └── pagination.py         ← Custom pagination
│
├── requirements/
│   ├── base.txt
│   ├── development.txt
│   └── production.txt
│
└── manage.py
```

### What Goes Where

| File | Contains |
|---|---|
| `models.py` | Database structure only |
| `serializers.py` | Input/output format, field validation |
| `views.py` | HTTP handling, calls services |
| `services.py` | Business logic, DB operations |
| `filters.py` | FilterSet classes |
| `utils.py` | Pure helper functions (no DB) |
| `permissions.py` | Custom permission classes |
| `admin.py` | Admin configuration |

---

## 8.2 Keeping Views Clean

### Anti-pattern (Fat View — BAD)

```python
# ❌ Don't do this
class PlaceOrderView(APIView):
    def post(self, request):
        # 50 lines of business logic directly in the view
        cart = Cart.objects.get(user=request.user)
        total = 0
        for item in cart.items.all():
            if item.product.stock < item.quantity:
                return Response({"error": "Out of stock"}, status=400)
            total += item.price * item.quantity
        order = Order.objects.create(...)
        # ... 30 more lines
```

### Correct Pattern (Thin View — GOOD)

```python
# ✅ Do this
class PlaceOrderView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        serializer = PlaceOrderSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        order = OrderService.place_order(
            user=request.user,
            **serializer.validated_data
        )

        return success_response(
            data=OrderSerializer(order).data,
            message="Order placed successfully.",
            status_code=201
        )
```

---

# 9. Middleware — Basic Overview

### Simple Definition
Middleware is a hook that runs before and/or after every request. It's a layer that wraps all views.

### Built-in Middleware (settings.py)

```python
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",      # HTTPS, headers
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",          # CSRF protection
    "django.contrib.auth.middleware.AuthenticationMiddleware",  # Attaches request.user
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]
```

### Custom Middleware Example

```python
# core/middleware.py
import logging
import time

logger = logging.getLogger(__name__)

class RequestLoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        start = time.time()

        response = self.get_response(request)

        duration = time.time() - start
        logger.info(
            f"{request.method} {request.path} "
            f"→ {response.status_code} "
            f"[{duration:.3f}s] "
            f"user={request.user}"
        )

        return response
```

```python
# settings.py
MIDDLEWARE = [
    ...
    "core.middleware.RequestLoggingMiddleware",
]
```

### Common Use Cases for Middleware

| Use Case | What to Do |
|---|---|
| Request logging | Log method, path, status, time |
| Maintenance mode | Return 503 for all requests |
| Tenant identification | Identify tenant from subdomain |
| Rate limiting | Block too many requests from one IP |
| CORS headers | Use `django-cors-headers` |

---

# 10. API Documentation

## 10.1 Postman / Hoppscotch Documentation

### Postman Collection Structure

```
📁 E-Commerce API
├── 📁 Auth
│   ├── POST /api/v1/auth/register/
│   ├── POST /api/v1/auth/login/
│   ├── POST /api/v1/auth/logout/
│   └── POST /api/v1/auth/refresh-token/
├── 📁 Products
│   ├── GET  /api/v1/products/
│   ├── POST /api/v1/products/
│   ├── GET  /api/v1/products/{id}/
│   ├── PATCH /api/v1/products/{id}/
│   └── DELETE /api/v1/products/{id}/
└── 📁 Orders
    ├── POST /api/v1/orders/
    └── GET  /api/v1/orders/{id}/
```

### Environment Variables in Postman

```
base_url  = http://localhost:8000
token     = (set after login)
```

### Auto-set Token After Login

In Postman → Tests tab for the login request:

```javascript
const res = pm.response.json();
pm.environment.set("token", res.data.access);
```

Then use in headers:
```
Authorization: Bearer {{token}}
```

---

## 10.2 Defining API Flow

### Document Login → Protected API flow

```
FLOW: Place an Order
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Step 1: Register / Login
  POST /api/v1/auth/login/
  Body: { "email": "...", "password": "..." }
  Response: { "access": "jwt_token", "refresh": "..." }
  → Save `access` token

Step 2: Browse Products
  GET /api/v1/products/
  Headers: Authorization: Bearer <token>
  Query Params: ?category=shoes&min_price=500

Step 3: Place Order
  POST /api/v1/orders/
  Headers: Authorization: Bearer <token>
  Body: { "items": [...], "payment_method": "upi" }
  Response: { "id": "uuid", "status": "pending", ... }

Step 4: Track Order
  GET /api/v1/orders/{id}/
  Headers: Authorization: Bearer <token>
```

---

## 10.3 Documenting Filters, Request Body, and Responses

### Auto Documentation with drf-spectacular

```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS = ["drf_spectacular", ...]

REST_FRAMEWORK = {
    "DEFAULT_SCHEMA_CLASS": "drf_spectacular.openapi.AutoSchema",
}

SPECTACULAR_SETTINGS = {
    "TITLE": "E-Commerce API",
    "DESCRIPTION": "API documentation for the E-Commerce platform.",
    "VERSION": "1.0.0",
}
```

```python
# config/urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView

urlpatterns = [
    path("api/schema/",  SpectacularAPIView.as_view(),        name="schema"),
    path("api/docs/",    SpectacularSwaggerView.as_view(url_name="schema"), name="docs"),
]
```

### Annotating Views for Better Docs

```python
from drf_spectacular.utils import extend_schema, OpenApiParameter

class ProductViewSet(ModelViewSet):

    @extend_schema(
        summary="List all products",
        description="Returns a paginated list of active products. Supports filtering and search.",
        parameters=[
            OpenApiParameter("min_price", float, description="Minimum price filter"),
            OpenApiParameter("max_price", float, description="Maximum price filter"),
            OpenApiParameter("search",    str,   description="Search in name and description"),
        ],
        responses={200: ProductSerializer(many=True)}
    )
    def list(self, request, *args, **kwargs):
        return super().list(request, *args, **kwargs)

    @extend_schema(
        summary="Create a product",
        responses={201: ProductSerializer}
    )
    def create(self, request, *args, **kwargs):
        return super().create(request, *args, **kwargs)
```

### Document in Postman (Manual)

For each API, document:

```
Endpoint : POST /api/v1/orders/
Auth     : Bearer Token required

Headers:
  Content-Type : application/json
  Authorization: Bearer {{token}}

Request Body:
  {
    "payment_method": "upi",       // Required. Choices: card, upi, cod, wallet
    "items": [
      {
        "product_id": "uuid",      // Required
        "quantity": 2              // Required. Min: 1
      }
    ]
  }

Success Response (201):
  {
    "success": true,
    "message": "Order placed successfully.",
    "data": {
      "id": "uuid",
      "status": "pending",
      "total_amount": "999.00",
      "created_at": "2024-01-15T10:30:00Z"
    }
  }

Error Response (400):
  {
    "success": false,
    "message": "Validation failed.",
    "errors": {
      "payment_method": ["This field is required."]
    }
  }

Filters:
  ?status=pending
  ?date_from=2024-01-01
  ?date_to=2024-12-31
  ?ordering=-created_at
```

---

## Quick Reference: DRF Settings Cheatsheet

```python
# settings.py
REST_FRAMEWORK = {
    # Auth
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ],

    # Permissions
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticated",
    ],

    # Filtering
    "DEFAULT_FILTER_BACKENDS": [
        "django_filters.rest_framework.DjangoFilterBackend",
        "rest_framework.filters.SearchFilter",
        "rest_framework.filters.OrderingFilter",
    ],

    # Pagination
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 20,

    # Exception Handler
    "EXCEPTION_HANDLER": "core.exceptions.custom_exception_handler",

    # Schema
    "DEFAULT_SCHEMA_CLASS": "drf_spectacular.openapi.AutoSchema",
}
```

---

*Notes generated for complete DRF mastery — Models → Serializers → Views → Responses → URLs → Filtering → Admin → Structure → Middleware → Documentation*
