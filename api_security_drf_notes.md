# API Security & Django REST Framework — Complete Notes

## Table of Contents

### Level 1: Fundamentals
- [1.1 What is API Security and Attack Surface](#11-what-is-api-security-and-attack-surface)
- [1.2 HTTP vs HTTPS and TLS Basics](#12-http-vs-https-and-tls-basics)
- [1.3 Authentication vs Authorization](#13-authentication-vs-authorization)
- [1.4 API Keys and Their Pros and Cons](#14-api-keys-and-their-pros-and-cons)
- [1.5 Basic Rate Limiting](#15-basic-rate-limiting)
- [1.6 Input Validation and Sanitization](#16-input-validation-and-sanitization)
- [1.7 OWASP Top 10 Overview](#17-owasp-top-10-overview)

### Level 2: Core Security Mechanisms
- [2.1 JWT — Access and Refresh Tokens](#21-jwt--access-and-refresh-tokens)
- [2.2 OAuth 2.0 Flows](#22-oauth-20-flows)
- [2.3 OpenID Connect](#23-openid-connect)
- [2.4 Role-Based Access Control (RBAC)](#24-role-based-access-control-rbac)
- [2.5 Attribute-Based Access Control (ABAC)](#25-attribute-based-access-control-abac)
- [2.6 CORS Deep Understanding](#26-cors-deep-understanding)
- [2.7 Secure Headers](#27-secure-headers)
- [2.8 API Gateway Security Basics](#28-api-gateway-security-basics)

### Level 3: Advanced API Security
- [3.1 Token Replay Attacks and Prevention](#31-token-replay-attacks-and-prevention)
- [3.2 CSRF vs XSS in APIs](#32-csrf-vs-xss-in-apis)
- [3.3 API Abuse and Business Logic Attacks](#33-api-abuse-and-business-logic-attacks)
- [3.4 Mass Assignment Vulnerability](#34-mass-assignment-vulnerability)
- [3.5 Broken Object Level Authorization (BOLA)](#35-broken-object-level-authorization-bola)
- [3.6 Broken Function Level Authorization (BFLA)](#36-broken-function-level-authorization-bfla)
- [3.7 Rate Limiting Bypass Techniques](#37-rate-limiting-bypass-techniques)
- [3.8 Data Exfiltration via APIs](#38-data-exfiltration-via-apis)
- [3.9 Secure Pagination and Filtering Attacks](#39-secure-pagination-and-filtering-attacks)
- [3.10 API Versioning Security Risks](#310-api-versioning-security-risks)

### Level 4: Expert and Real-World Security
- [4.1 Zero Trust Architecture for APIs](#41-zero-trust-architecture-for-apis)
- [4.2 Mutual TLS (mTLS)](#42-mutual-tls-mtls)
- [4.3 HMAC Signatures](#43-hmac-signatures)
- [4.4 API Gateway and WAF Integration](#44-api-gateway-and-waf-integration)
- [4.5 DDoS Protection Strategies](#45-ddos-protection-strategies)
- [4.6 Secrets Management](#46-secrets-management)
- [4.7 API Threat Modeling](#47-api-threat-modeling)
- [4.8 Schema Validation](#48-schema-validation)
- [4.9 GraphQL Security](#49-graphql-security)
- [4.10 gRPC Security Considerations](#410-grpc-security-considerations)

---

# Level 1: Fundamentals

## 1.1 What is API Security and Attack Surface

### Simple Definition
An **API (Application Programming Interface)** is a way for two programs to talk to each other. **API Security** means protecting these communication channels from bad actors who want to steal data, break the system, or misuse it.

The **attack surface** is the total number of entry points (endpoints, parameters, headers, etc.) that an attacker can try to exploit.

### Why It Is Used
- APIs expose your backend logic to the internet
- Without security, anyone can read, modify, or delete your data
- Protects user privacy and business data

### Types of Attack Surfaces
- **Network attack surface** — open ports, unencrypted traffic
- **Application attack surface** — API endpoints, input fields, headers
- **Authentication attack surface** — login, token endpoints
- **Third-party attack surface** — external libraries, integrations

### Advantages and Disadvantages

| Advantages of Securing APIs | Disadvantages (Costs) |
|---|---|
| Protects user data | Takes time to implement |
| Prevents financial loss | Can slow development |
| Builds user trust | Requires ongoing maintenance |

### Tips and Tricks
- Map every endpoint you expose — that's your attack surface
- Remove unused endpoints immediately
- Never expose internal APIs to the public without a gateway

### Best Practices
- Audit all endpoints regularly
- Use principle of least privilege
- Document every API and keep it updated
- Scan with tools like OWASP ZAP or Postman security tests

### How It Integrates with DRF

**Setup:**
```bash
pip install djangorestframework
```

**settings.py:**
```python
INSTALLED_APPS = [
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',  # Secure by default
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
    ],
}
```

**models.py:**
```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    owner = models.ForeignKey('auth.User', on_delete=models.CASCADE)
```

**serializers.py:**
```python
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price']  # Never expose all fields blindly
```

**views.py:**
```python
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from .models import Product
from .serializers import ProductSerializer

class ProductViewSet(viewsets.ModelViewSet):
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        # Only return the current user's products — reduce attack surface
        return Product.objects.filter(owner=self.request.user)
```

**urls.py:**
```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import ProductViewSet

router = DefaultRouter()
router.register('products', ProductViewSet, basename='product')

urlpatterns = [
    path('api/', include(router.urls)),
]
```

---

## 1.2 HTTP vs HTTPS and TLS Basics

### Simple Definition
- **HTTP** = HyperText Transfer Protocol. Data travels as plain text — anyone on the network can read it.
- **HTTPS** = HTTP + Security. Data is encrypted before sending, so no one can read it in transit.
- **TLS (Transport Layer Security)** = The encryption protocol that makes HTTPS work.

### Why It Is Used
Without HTTPS, passwords and tokens sent over the network can be stolen (called a **Man-in-the-Middle attack**).

### How TLS Works (Simple)
1. Client connects to server
2. Server sends its **SSL/TLS certificate** (proof of identity)
3. Client verifies the certificate
4. Both agree on encryption keys (called **TLS Handshake**)
5. All data is now encrypted

### Types of TLS
- **TLS 1.2** — still common, mostly secure
- **TLS 1.3** — latest, faster and more secure (recommended)

### Advantages and Disadvantages

| HTTPS Advantages | Disadvantages |
|---|---|
| Data encrypted in transit | Slight performance overhead |
| Prevents eavesdropping | Requires SSL certificate |
| Builds user trust | Certificate must be renewed |

### Tips and Tricks
- Use free certificates from **Let's Encrypt**
- Disable TLS 1.0 and 1.1 — they are outdated and insecure
- Always redirect HTTP → HTTPS

### Best Practices
- Enforce HTTPS at the server/load balancer level
- Use HSTS header to prevent downgrade attacks
- Renew certificates before expiry (automate with certbot)

### How It Integrates with DRF

**settings.py:**
```python
# Force HTTPS in production
SECURE_SSL_REDIRECT = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True

# HSTS — tell browsers to always use HTTPS
SECURE_HSTS_SECONDS = 31536000  # 1 year
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
```

**Real API Example:**
```
# Insecure — DO NOT USE
GET http://api.example.com/users/1/

# Secure — Always use this
GET https://api.example.com/users/1/
```

---

## 1.3 Authentication vs Authorization

### Simple Definition
- **Authentication** = Verifying WHO you are (login with username/password)
- **Authorization** = Verifying WHAT you can do (can you access this resource?)

> Think of it like a hotel: Authentication = showing your ID at reception. Authorization = your room key only opens your room.

### Why It Is Used
- Without authentication, anyone can access your API
- Without authorization, any logged-in user can access any data

### Types

**Authentication:**
- Basic Auth (username + password in header)
- Token Auth (API key or JWT)
- Session Auth (cookie-based)
- OAuth (third-party login)

**Authorization:**
- Role-based (admin, user, moderator)
- Permission-based (can_read, can_write)
- Object-level (only your own data)

### Advantages and Disadvantages

| Authentication | Authorization |
|---|---|
| Proves identity | Controls access |
| Required first | Applied after auth |
| Stateless with JWT | Can be complex with ABAC |

### Tips and Tricks
- Always authenticate BEFORE checking authorization
- Never skip authorization — just because a user is logged in doesn't mean they can do everything
- Use DRF's built-in permission classes

### Best Practices
- Separate authentication logic from business logic
- Apply authorization at the view AND queryset level
- Log all authentication failures

### How It Integrates with DRF

**views.py:**
```python
from rest_framework.permissions import IsAuthenticated, IsAdminUser
from rest_framework.views import APIView
from rest_framework.response import Response

class PublicView(APIView):
    # No authentication needed
    permission_classes = []

    def get(self, request):
        return Response({'message': 'Anyone can see this'})


class PrivateView(APIView):
    # Must be logged in (Authentication)
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({'message': f'Hello {request.user.username}'})


class AdminView(APIView):
    # Must be admin (Authorization)
    permission_classes = [IsAuthenticated, IsAdminUser]

    def get(self, request):
        return Response({'message': 'Admin only area'})
```

**Custom Permission (Authorization):**
```python
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):
    """Allow access only to the object's owner."""

    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user
```

---

## 1.4 API Keys and Their Pros and Cons

### Simple Definition
An **API Key** is a unique secret string (like a password) that a client sends with each request to prove it is allowed to access the API.

```
GET /api/products/
Headers: X-API-Key: abc123xyz456
```

### Why It Is Used
- Identify which application or user is making a request
- Control and monitor API usage
- Simple to implement and use

### Types
- **Public API keys** — shared with frontend apps (less sensitive)
- **Secret API keys** — kept on servers only (highly sensitive)
- **Scoped API keys** — limited to specific actions (e.g., read-only)

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Simple to implement | No built-in expiry |
| Easy to revoke | Easy to leak (in code, logs) |
| Stateless | Not tied to a specific user |
| Works for machine-to-machine | Harder to rotate at scale |

### Tips and Tricks
- Store API keys hashed in the database (like passwords)
- Always send API keys in **headers**, not URLs (URLs get logged)
- Rotate API keys regularly
- Scope keys to minimum needed permissions

### Best Practices
- Never commit API keys to version control (use `.env` files)
- Use environment variables for all secrets
- Log API key usage for auditing
- Implement key expiry

### How It Integrates with DRF

**models.py:**
```python
import secrets
from django.db import models

class APIKey(models.Model):
    user = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    key = models.CharField(max_length=64, unique=True)
    name = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)
    is_active = models.BooleanField(default=True)

    @classmethod
    def generate_key(cls):
        return secrets.token_urlsafe(32)
```

**authentication.py:**
```python
from rest_framework.authentication import BaseAuthentication
from rest_framework.exceptions import AuthenticationFailed
from .models import APIKey

class APIKeyAuthentication(BaseAuthentication):
    def authenticate(self, request):
        api_key = request.headers.get('X-API-Key')
        if not api_key:
            return None  # Let other authenticators try

        try:
            key_obj = APIKey.objects.get(key=api_key, is_active=True)
        except APIKey.DoesNotExist:
            raise AuthenticationFailed('Invalid API Key')

        return (key_obj.user, key_obj)
```

**settings.py:**
```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'myapp.authentication.APIKeyAuthentication',
    ]
}
```

**views.py:**
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated

class SecureAPIView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({'data': 'Protected data', 'user': request.user.username})
```

---

## 1.5 Basic Rate Limiting

### Simple Definition
**Rate limiting** means restricting how many requests a user or IP can make in a certain time period. For example: max 100 requests per minute.

### Why It Is Used
- Prevents API abuse (someone hammering your server)
- Protects against DDoS attacks
- Ensures fair usage for all users
- Reduces server costs

### Types
- **Fixed Window** — 100 requests per minute, resets every minute
- **Sliding Window** — rolling 1-minute window (more fair)
- **Token Bucket** — tokens refill over time (allows bursts)
- **Leaky Bucket** — requests processed at constant rate

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Protects server resources | Can block legitimate users |
| Prevents abuse | Needs Redis for distributed apps |
| Easy to implement in DRF | Requires tuning |

### Tips and Tricks
- Return `HTTP 429 Too Many Requests` when limit hit
- Include `Retry-After` header in response
- Apply different limits for different user types (anonymous vs authenticated)
- Use Redis for distributed rate limiting

### Best Practices
- Rate limit by user AND by IP
- Set stricter limits for sensitive endpoints (login, password reset)
- Monitor rate limit hits — they signal abuse attempts

### How It Integrates with DRF

**settings.py:**
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',     # Anonymous users: 100 requests/day
        'user': '1000/day',    # Logged-in users: 1000 requests/day
    }
}
```

**Custom Throttle (stricter for sensitive endpoints):**
```python
from rest_framework.throttling import UserRateThrottle

class LoginThrottle(UserRateThrottle):
    scope = 'login'
```

**settings.py (add scope):**
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day',
        'login': '5/minute',   # Only 5 login attempts per minute
    }
}
```

**views.py:**
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .throttling import LoginThrottle

class LoginView(APIView):
    throttle_classes = [LoginThrottle]

    def post(self, request):
        # Login logic here
        return Response({'token': 'abc123'})
```

---

## 1.6 Input Validation and Sanitization

### Simple Definition
- **Validation** = Checking that input data is correct (e.g., email must have `@`)
- **Sanitization** = Cleaning input data to remove dangerous content (e.g., strip HTML tags)

> Never trust data coming from the user. Always validate AND sanitize.

### Why It Is Used
- Prevents SQL Injection attacks
- Prevents XSS (Cross-Site Scripting) attacks
- Ensures data integrity in your database
- Prevents server crashes from malformed data

### Types of Validation
- **Type validation** — is this a number? a string?
- **Format validation** — valid email? valid URL?
- **Range validation** — is age between 0 and 150?
- **Business rule validation** — is the product in stock?

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Prevents many attacks | Can be over-restrictive |
| Keeps data clean | Takes time to write rules |
| DRF Serializers handle it | Complex nested validation is hard |

### Tips and Tricks
- In DRF, serializers handle most validation automatically
- Use `validate_<fieldname>()` for field-level validation
- Use `validate()` for cross-field validation
- Always sanitize HTML content with `bleach` library

### Best Practices
- Validate at the API layer AND the model layer
- Use allowlists (accepted values) not denylists (rejected values)
- Never pass raw user input to database queries

### How It Integrates with DRF

**serializers.py:**
```python
from rest_framework import serializers
import bleach  # pip install bleach

class CommentSerializer(serializers.Serializer):
    username = serializers.CharField(max_length=50)
    email = serializers.EmailField()
    age = serializers.IntegerField(min_value=0, max_value=150)
    comment = serializers.CharField(max_length=1000)

    def validate_username(self, value):
        """Field-level validation"""
        if ' ' in value:
            raise serializers.ValidationError("Username cannot contain spaces")
        return value.lower()  # Normalize to lowercase

    def validate_comment(self, value):
        """Sanitize HTML to prevent XSS"""
        return bleach.clean(value, strip=True)

    def validate(self, data):
        """Cross-field validation"""
        if data['age'] < 18 and 'adult_content' in data:
            raise serializers.ValidationError("Must be 18+ for adult content")
        return data
```

**views.py:**
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .serializers import CommentSerializer

class CommentView(APIView):
    def post(self, request):
        serializer = CommentSerializer(data=request.data)
        if serializer.is_valid():
            # Safe to use validated_data
            return Response(serializer.validated_data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

---

## 1.7 OWASP Top 10 Overview

### Simple Definition
**OWASP (Open Web Application Security Project)** is a nonprofit that publishes the **Top 10 most critical API security risks**. Think of it as the industry's most important security checklist.

### Why It Is Used
- Industry standard for security audits
- Helps developers understand the most common vulnerabilities
- Used by security teams to test APIs

### The OWASP API Security Top 10

| # | Risk | Simple Explanation |
|---|---|---|
| 1 | **Broken Object Level Authorization (BOLA)** | Accessing another user's data by changing an ID |
| 2 | **Broken Authentication** | Weak login, no token expiry |
| 3 | **Broken Object Property Level Authorization** | Seeing/editing fields you shouldn't |
| 4 | **Unrestricted Resource Consumption** | No rate limiting → server overload |
| 5 | **Broken Function Level Authorization (BFLA)** | Regular user accessing admin functions |
| 6 | **Unrestricted Access to Sensitive Business Flows** | Automating actions that should be manual |
| 7 | **Server-Side Request Forgery (SSRF)** | Tricking server to fetch internal resources |
| 8 | **Security Misconfiguration** | Debug mode on, default passwords, open CORS |
| 9 | **Improper Inventory Management** | Old/forgotten API versions still running |
| 10 | **Unsafe Consumption of APIs** | Trusting third-party API data without validation |

### Best Practices
- Review your API against each OWASP risk regularly
- Use automated scanners (OWASP ZAP)
- Fix issues by priority (BOLA and auth issues first)

### How It Integrates with DRF

**Quick DRF Checklist against OWASP Top 10:**
```python
# 1. BOLA — Always filter by owner
def get_queryset(self):
    return Order.objects.filter(user=self.request.user)  # ✅

# 2. Authentication — Use strong auth
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': ['rest_framework_simplejwt.authentication.JWTAuthentication']
}

# 3. Property Auth — Use read_only_fields or separate serializers
class Meta:
    read_only_fields = ['is_admin', 'balance']  # ✅

# 4. Rate Limiting — Use throttling
'DEFAULT_THROTTLE_CLASSES': ['rest_framework.throttling.UserRateThrottle']

# 5. BFLA — Use permission classes
permission_classes = [IsAuthenticated, IsAdminUser]

# 8. Misconfiguration — Disable debug in production
DEBUG = False  # ✅ in production settings
```

---

# Level 2: Core Security Mechanisms

## 2.1 JWT — Access and Refresh Tokens

### Simple Definition
**JWT (JSON Web Token)** is a compact, self-contained token that stores user information. When a user logs in, the server gives them a JWT. They include it in every future request to prove who they are — the server doesn't need to check a database each time.

**Structure of JWT:**
```
header.payload.signature
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxfQ.abc123
```
- **Header** — algorithm type
- **Payload** — user data (id, roles, expiry)
- **Signature** — proves it wasn't tampered with

### Why It Is Used
- **Stateless** — server doesn't store session data
- **Scalable** — works across multiple servers
- **Fast** — no database lookup needed per request

### Types
- **Access Token** — short-lived (15 min). Used for API requests.
- **Refresh Token** — long-lived (7 days). Used to get new access tokens.

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Stateless and scalable | Cannot be revoked (until expiry) |
| Self-contained | Payload is base64-encoded (readable) |
| Works across services | Larger than session IDs |

### Tips and Tricks
- Access tokens should expire in 5-15 minutes
- Refresh tokens should expire in 7-30 days
- Store access tokens in memory, refresh tokens in `httpOnly` cookies
- Never store JWTs in `localStorage` (XSS risk)

### Best Practices
- Always verify the JWT signature server-side
- Check token expiry on every request
- Implement refresh token rotation (each use generates a new one)
- Maintain a refresh token blocklist for logout

### How It Integrates with DRF

**Install:**
```bash
pip install djangorestframework-simplejwt
```

**settings.py:**
```python
from datetime import timedelta

INSTALLED_APPS = ['rest_framework_simplejwt']

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ]
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=15),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,       # New refresh token on each use
    'BLACKLIST_AFTER_ROTATION': True,    # Old token becomes invalid
    'ALGORITHM': 'HS256',
    'AUTH_HEADER_TYPES': ('Bearer',),
}
```

**urls.py:**
```python
from django.urls import path
from rest_framework_simplejwt.views import (
    TokenObtainPairView,   # Login → get access + refresh token
    TokenRefreshView,      # Use refresh token → get new access token
    TokenBlacklistView,    # Logout → blacklist refresh token
)

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view()),
    path('api/token/refresh/', TokenRefreshView.as_view()),
    path('api/token/blacklist/', TokenBlacklistView.as_view()),
]
```

**views.py:**
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated

class ProfileView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({
            'user': request.user.username,
            'email': request.user.email,
        })
```

**Real API Example:**
```bash
# Step 1: Login and get tokens
POST /api/token/
Body: {"username": "john", "password": "secret"}
Response: {"access": "eyJ...", "refresh": "eyJ..."}

# Step 2: Use access token
GET /api/profile/
Headers: Authorization: Bearer eyJ...

# Step 3: Refresh when access token expires
POST /api/token/refresh/
Body: {"refresh": "eyJ..."}
Response: {"access": "eyJ...new"}
```

---

## 2.2 OAuth 2.0 Flows

### Simple Definition
**OAuth 2.0** is a protocol that lets users give apps limited access to their account on another service — **without sharing their password**.

> Example: "Login with Google" — you don't give your Gmail password to the app; Google handles it and just tells the app "yes, this user is valid."

### Why It Is Used
- Delegated authorization (one service authorizing another)
- Avoids storing passwords in every app
- Industry standard for third-party integrations

### Types (Flows)

**1. Authorization Code Flow** (Most Secure — for web/mobile apps with a backend)
```
User → App → Redirect to Google → User approves → Google sends Code → 
App exchanges Code for Token → App accesses API
```

**2. Client Credentials Flow** (Machine-to-Machine — no user involved)
```
App → Send client_id + client_secret → Get Access Token → Access API
```

**3. PKCE (Proof Key for Code Exchange)** (For SPAs and mobile apps)
- Extension of Authorization Code flow for public clients

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| No password sharing | Complex to implement |
| Revocable access | Multiple flows to understand |
| Scoped permissions | Token management overhead |

### Tips and Tricks
- Use Authorization Code + PKCE for user-facing apps
- Use Client Credentials for backend services
- Always validate the `state` parameter to prevent CSRF
- Tokens should have minimal scopes

### Best Practices
- Never use Implicit Flow (deprecated and insecure)
- Store tokens securely (httpOnly cookies for web)
- Validate the `redirect_uri` strictly
- Use short-lived access tokens

### How It Integrates with DRF

**Install:**
```bash
pip install django-oauth-toolkit
```

**settings.py:**
```python
INSTALLED_APPS = [
    'oauth2_provider',
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'oauth2_provider.contrib.rest_framework.OAuth2Authentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
```

**urls.py:**
```python
from django.urls import path, include

urlpatterns = [
    path('o/', include('oauth2_provider.urls', namespace='oauth2_provider')),
    path('api/', include('myapp.urls')),
]
```

**views.py (protecting with scopes):**
```python
from oauth2_provider.contrib.rest_framework import TokenMatchesOASRequirements
from rest_framework.views import APIView
from rest_framework.response import Response

class UserDataView(APIView):
    required_scopes = ['read']  # Only allow tokens with 'read' scope

    def get(self, request):
        return Response({'data': 'Protected data'})
```

**Real API Flow:**
```bash
# Client Credentials Flow
POST /o/token/
Body: {
  "grant_type": "client_credentials",
  "client_id": "your_client_id",
  "client_secret": "your_secret"
}
Response: {"access_token": "abc...", "token_type": "Bearer", "expires_in": 3600}

# Use token
GET /api/data/
Headers: Authorization: Bearer abc...
```

---

## 2.3 OpenID Connect

### Simple Definition
**OpenID Connect (OIDC)** is a layer on top of OAuth 2.0 that adds **identity verification**. OAuth 2.0 handles authorization (can you access?), OIDC handles authentication (who are you?).

> OAuth 2.0 = Keys to the car. OIDC = Driver's license that proves who's driving.

### Why It Is Used
- Single Sign-On (SSO) — log in once, access many apps
- Standardized way to get user profile info
- "Login with Google/GitHub/Facebook" uses OIDC

### Key Difference from OAuth 2.0
- OIDC adds an **ID Token** (JWT with user identity info)
- OIDC provides a `/userinfo` endpoint
- Introduces claims: `sub`, `email`, `name`, `picture`

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Standardized identity layer | Adds complexity over plain OAuth |
| SSO support | Requires HTTPS |
| Rich user profile data | Token validation is critical |

### Tips and Tricks
- Always validate the ID token signature
- Check `aud` (audience) and `iss` (issuer) claims
- Use `nonce` to prevent replay attacks in OIDC

### Best Practices
- Use established providers (Auth0, Okta, Google, Keycloak)
- Don't build your own OIDC server unless necessary
- Validate all JWT claims on the server

### How It Integrates with DRF

**Install:**
```bash
pip install mozilla-django-oidc
```

**settings.py:**
```python
INSTALLED_APPS = ['mozilla_django_oidc']

AUTHENTICATION_BACKENDS = [
    'mozilla_django_oidc.auth.OIDCAuthenticationBackend',
]

OIDC_RP_CLIENT_ID = 'your-client-id'
OIDC_RP_CLIENT_SECRET = 'your-secret'
OIDC_OP_AUTHORIZATION_ENDPOINT = 'https://provider.com/auth'
OIDC_OP_TOKEN_ENDPOINT = 'https://provider.com/token'
OIDC_OP_USER_ENDPOINT = 'https://provider.com/userinfo'
OIDC_RP_SIGN_ALGO = 'RS256'
OIDC_OP_JWKS_ENDPOINT = 'https://provider.com/.well-known/jwks.json'
```

**urls.py:**
```python
from django.urls import path, include

urlpatterns = [
    path('oidc/', include('mozilla_django_oidc.urls')),
]
```

---

## 2.4 Role-Based Access Control (RBAC)

### Simple Definition
**RBAC** means assigning users to **roles** (like Admin, Editor, Viewer), and each role has specific **permissions**. Instead of assigning permissions to individual users, you assign them to roles.

> Hospital example: Doctors can see patient records. Receptionists can only see appointments. Admins can manage everything.

### Why It Is Used
- Easy to manage permissions at scale
- Add/remove a user from a role instead of editing individual permissions
- Clear separation of access levels

### Types
- **Flat RBAC** — simple roles with permissions
- **Hierarchical RBAC** — Admin > Manager > User (inherits lower role's permissions)
- **Constrained RBAC** — roles limited to certain conditions

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Easy to manage | Not flexible for complex rules |
| Clear access structure | Role explosion (too many roles) |
| Built into Django | Can't handle context-based rules |

### Tips and Tricks
- Use Django's built-in `groups` as roles
- Create custom permissions with `Meta.permissions`
- Use `@permission_required` or DRF's `DjangoModelPermissions`

### Best Practices
- Principle of least privilege — give minimum needed role
- Review roles regularly
- Don't create too many granular roles

### How It Integrates with DRF

**models.py:**
```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)

    class Meta:
        permissions = [
            ('can_publish', 'Can publish articles'),
            ('can_edit_any', 'Can edit any article'),
        ]
```

**permissions.py:**
```python
from rest_framework.permissions import BasePermission

class IsEditor(BasePermission):
    """Allow access only to users in the 'editors' group."""

    def has_permission(self, request, view):
        return request.user.groups.filter(name='editors').exists()


class IsAdminOrReadOnly(BasePermission):
    def has_permission(self, request, view):
        if request.method in ('GET', 'HEAD', 'OPTIONS'):
            return True
        return request.user.is_staff
```

**views.py:**
```python
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from .permissions import IsEditor, IsAdminOrReadOnly
from .models import Article
from .serializers import ArticleSerializer

class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def get_permissions(self):
        if self.action in ['create', 'update', 'partial_update', 'destroy']:
            return [IsAuthenticated(), IsEditor()]
        return [IsAuthenticated()]
```

**Setup roles via Django Admin or shell:**
```python
from django.contrib.auth.models import Group, Permission, User

# Create groups (roles)
editor_group, _ = Group.objects.get_or_create(name='editors')
admin_group, _ = Group.objects.get_or_create(name='admins')

# Add permissions to groups
publish_perm = Permission.objects.get(codename='can_publish')
editor_group.permissions.add(publish_perm)

# Add user to group
user = User.objects.get(username='john')
user.groups.add(editor_group)
```

---

## 2.5 Attribute-Based Access Control (ABAC)

### Simple Definition
**ABAC** gives or denies access based on **attributes** — properties of the user, resource, and environment. It's more flexible than RBAC.

> RBAC: "Doctors can see all patient records."
> ABAC: "Doctors can see patient records ONLY for patients assigned to them, AND only during working hours."

### Why It Is Used
- When RBAC is too simple for your rules
- Context-aware access (time, location, device)
- Fine-grained control over resources

### Key Attributes
- **User attributes** — role, department, clearance level
- **Resource attributes** — type, owner, sensitivity level
- **Environment attributes** — time, IP address, location

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Very flexible | Complex to implement |
| Context-aware rules | Harder to audit |
| No role explosion | Performance overhead |

### Tips and Tricks
- Start with RBAC and add ABAC only when needed
- Use Python rules/policies as functions
- Consider using a library like `py-abac`

### Best Practices
- Keep policies simple and testable
- Log all access decisions
- Test edge cases thoroughly

### How It Integrates with DRF

**permissions.py:**
```python
from rest_framework.permissions import BasePermission
from datetime import datetime

class ABACPermission(BasePermission):
    """
    ABAC: Allow access if:
    - User is in 'doctors' group AND
    - Patient is assigned to the doctor AND
    - Request is during business hours (9AM-5PM)
    """

    def has_object_permission(self, request, view, obj):
        user = request.user
        current_hour = datetime.now().hour

        # User attribute check
        is_doctor = user.groups.filter(name='doctors').exists()

        # Resource attribute check
        is_assigned = obj.assigned_doctor == user

        # Environment attribute check
        is_business_hours = 9 <= current_hour < 17

        return is_doctor and is_assigned and is_business_hours


class SensitiveDataPermission(BasePermission):
    """Allow access based on user clearance vs data sensitivity."""

    def has_object_permission(self, request, view, obj):
        user_clearance = getattr(request.user, 'clearance_level', 0)
        data_sensitivity = getattr(obj, 'sensitivity_level', 0)
        return user_clearance >= data_sensitivity
```

**views.py:**
```python
from rest_framework import generics
from .permissions import ABACPermission
from .models import PatientRecord

class PatientRecordView(generics.RetrieveAPIView):
    queryset = PatientRecord.objects.all()
    permission_classes = [ABACPermission]
```

---

## 2.6 CORS Deep Understanding

### Simple Definition
**CORS (Cross-Origin Resource Sharing)** is a browser security feature that blocks web pages from making requests to a different domain than where the page came from.

> Your frontend is at `http://app.com`. Your API is at `http://api.com`. The browser will BLOCK this request by default — that's CORS.

### Why It Is Used
- Prevents malicious websites from making requests using your logged-in session
- Browser-enforced (not server-enforced)
- Allows trusted domains to access your API

### Key Headers

| Header | Purpose |
|---|---|
| `Access-Control-Allow-Origin` | Which origins can access |
| `Access-Control-Allow-Methods` | Which HTTP methods are allowed |
| `Access-Control-Allow-Headers` | Which request headers are allowed |
| `Access-Control-Allow-Credentials` | Allow cookies/auth headers |
| `Access-Control-Max-Age` | How long to cache preflight |

### Preflight Request
For non-simple requests, the browser first sends an `OPTIONS` request to check if CORS is allowed. This is called a **preflight**.

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Protects against CSRF | Confusing to configure |
| Controlled API access | Easy to misconfigure |
| Browser-native protection | Only works in browsers |

### Tips and Tricks
- NEVER use `Access-Control-Allow-Origin: *` with credentials
- Be specific about allowed origins in production
- CORS errors are browser-side — check browser console

### Best Practices
- Whitelist specific origins
- Don't allow `*` in production
- Use `django-cors-headers` for DRF

### How It Integrates with DRF

**Install:**
```bash
pip install django-cors-headers
```

**settings.py:**
```python
INSTALLED_APPS = [
    'corsheaders',
    'rest_framework',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # Must be first
    'django.middleware.common.CommonMiddleware',
    # ... rest of middleware
]

# Development (allow all)
CORS_ALLOW_ALL_ORIGINS = True  # Only in development!

# Production (specific origins)
CORS_ALLOWED_ORIGINS = [
    "https://app.example.com",
    "https://admin.example.com",
]

CORS_ALLOW_CREDENTIALS = True  # Allow cookies

CORS_ALLOW_METHODS = [
    'DELETE', 'GET', 'OPTIONS', 'PATCH', 'POST', 'PUT',
]

CORS_ALLOW_HEADERS = [
    'accept', 'authorization', 'content-type', 'x-csrftoken',
]
```

---

## 2.7 Secure Headers

### Simple Definition
**Secure Headers** are HTTP response headers that tell the browser how to behave securely when displaying your content. They protect against many common attacks.

### Why It Is Used
- Prevents XSS, clickjacking, data sniffing
- Browser enforces them automatically
- Easy security wins with minimal code

### Important Headers

| Header | Purpose | Example Value |
|---|---|---|
| `Strict-Transport-Security (HSTS)` | Force HTTPS | `max-age=31536000; includeSubDomains` |
| `Content-Security-Policy (CSP)` | Restrict resource loading | `default-src 'self'` |
| `X-Frame-Options` | Prevent clickjacking | `DENY` |
| `X-Content-Type-Options` | Prevent MIME sniffing | `nosniff` |
| `Referrer-Policy` | Control referrer info | `strict-origin-when-cross-origin` |
| `Permissions-Policy` | Restrict browser features | `geolocation=()` |

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Easy to add | CSP can break functionality |
| Browser-enforced | Requires testing each header |
| Prevents multiple attacks | Over-restrictive can hurt UX |

### Tips and Tricks
- Test CSP in report-only mode first: `Content-Security-Policy-Report-Only`
- Use `securityheaders.com` to check your headers
- Don't set `X-Powered-By` header (reveals tech stack)

### Best Practices
- Set all security headers in production
- Test headers don't break your frontend
- Use CSP to restrict inline scripts

### How It Integrates with DRF

**settings.py:**
```python
# HSTS
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# Other security settings
SECURE_CONTENT_TYPE_NOSNIFF = True  # X-Content-Type-Options: nosniff
X_FRAME_OPTIONS = 'DENY'            # X-Frame-Options: DENY
SECURE_BROWSER_XSS_FILTER = True
```

**middleware.py (Custom security headers):**
```python
class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        response['Content-Security-Policy'] = "default-src 'self'"
        response['Referrer-Policy'] = 'strict-origin-when-cross-origin'
        response['Permissions-Policy'] = 'geolocation=(), microphone=()'
        response['X-Content-Type-Options'] = 'nosniff'
        return response
```

**settings.py (add middleware):**
```python
MIDDLEWARE = [
    'myapp.middleware.SecurityHeadersMiddleware',
    # ... rest
]
```

**Install django-csp for better CSP management:**
```bash
pip install django-csp
```
```python
INSTALLED_APPS = ['csp']
MIDDLEWARE = ['csp.middleware.CSPMiddleware']

CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'",)
CSP_IMG_SRC = ("'self'", "https://cdn.example.com")
```

---

## 2.8 API Gateway Security Basics

### Simple Definition
An **API Gateway** sits in front of your APIs and acts as the single entry point. It handles authentication, rate limiting, logging, and routing — so your actual APIs don't have to.

> Think of it like a hotel reception: all guests go through reception first. Reception verifies identity, hands out keys, and directs people to the right floor.

### Why It Is Used
- Centralized security enforcement
- Single place to add auth, rate limiting, logging
- Hides internal service architecture
- Load balancing and routing

### Features of API Gateways
- Authentication/Authorization
- Rate limiting
- SSL termination
- Request/response transformation
- Logging and monitoring
- IP allowlisting/blocklisting

### Popular API Gateways
- **AWS API Gateway**
- **Kong**
- **NGINX**
- **Traefik**
- **Azure API Management**

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Centralized security | Single point of failure |
| Reduces code duplication | Adds latency |
| Easy monitoring | Adds operational complexity |

### Tips and Tricks
- Let the gateway handle SSL termination
- Apply rate limiting at gateway level before traffic reaches Django
- Use gateway-level IP blocking for known bad IPs

### Best Practices
- Don't rely solely on gateway — still secure your DRF views
- Enable access logging at gateway level
- Use gateway health checks

### How It Integrates with DRF

**DRF behind NGINX as API Gateway:**

`nginx.conf`:
```nginx
server {
    listen 443 ssl;
    server_name api.example.com;

    # SSL termination at gateway
    ssl_certificate /etc/ssl/certs/cert.pem;
    ssl_certificate_key /etc/ssl/private/key.pem;

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_req zone=api burst=20 nodelay;

    location /api/ {
        # Forward to DRF
        proxy_pass http://django_app:8000;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # Security headers
        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;
    }
}
```

**DRF settings to trust gateway headers:**
```python
# Trust the gateway's IP forwarding
USE_X_FORWARDED_HOST = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
```

---

# Level 3: Advanced API Security

## 3.1 Token Replay Attacks and Prevention

### Simple Definition
A **token replay attack** happens when an attacker steals a valid token and reuses it to make requests — even after the legitimate user has logged out.

> Example: Attacker captures your Bearer token from network traffic, then uses it to access your account.

### Why It Is Used (Prevention)
- Stolen tokens should be rendered useless
- Logout should truly invalidate tokens
- Prevent unauthorized access even with stolen tokens

### Prevention Techniques
- **Short token expiry** — 15 minutes for access tokens
- **Token blacklisting** — block specific tokens after logout
- **Token binding** — tie token to client's IP/device
- **One-time tokens** — token used once then invalidated
- **Refresh token rotation** — old refresh token invalidated on use

### Advantages and Disadvantages

| Prevention Method | Pros | Cons |
|---|---|---|
| Short expiry | Simple | Frequent re-auth needed |
| Blacklisting | Immediate revocation | Requires shared storage (Redis) |
| Rotation | Auto-invalidates old tokens | Complexity increases |

### Tips and Tricks
- Use Redis for fast token blacklisting
- Implement jti (JWT ID) claim for unique token identification
- Always blacklist refresh tokens on logout

### Best Practices
- Keep access tokens short-lived (5-15 min)
- Implement token rotation for refresh tokens
- Maintain a token blocklist for revoked tokens
- Log all token usage

### How It Integrates with DRF

**settings.py (JWT with blacklisting):**
```python
INSTALLED_APPS = [
    'rest_framework_simplejwt.token_blacklist',
]

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=15),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
}
```

**views.py (Logout with blacklisting):**
```python
from rest_framework_simplejwt.tokens import RefreshToken
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from rest_framework import status

class LogoutView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        try:
            refresh_token = request.data['refresh']
            token = RefreshToken(refresh_token)
            token.blacklist()  # Invalidate the token
            return Response({'message': 'Logged out successfully'}, status=status.HTTP_200_OK)
        except Exception:
            return Response({'error': 'Invalid token'}, status=status.HTTP_400_BAD_REQUEST)
```

**Custom JWT with jti (unique token ID):**
```python
from rest_framework_simplejwt.tokens import AccessToken
import uuid

class CustomAccessToken(AccessToken):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.payload['jti'] = str(uuid.uuid4())  # Unique ID per token
```

---

## 3.2 CSRF vs XSS in APIs

### Simple Definition
- **CSRF (Cross-Site Request Forgery)** — Tricks a logged-in user's browser into making an unwanted request to your API using their existing session/cookie.
- **XSS (Cross-Site Scripting)** — Injects malicious JavaScript into your website/API response, which runs in the victim's browser.

> CSRF = Someone else uses YOUR browser to do something you didn't intend.
> XSS = Attacker plants code in YOUR site that runs when others visit it.

### CSRF in APIs
- Mainly affects **cookie-based authentication**
- JWT in Authorization header is **not vulnerable to CSRF**
- Cookie-based sessions are vulnerable

### XSS in APIs
- When API returns user-controlled data that gets rendered as HTML
- Can steal tokens from `localStorage`
- Prevented by Content Security Policy and output encoding

### Mitigation

**CSRF:**
- Use JWT with `Authorization: Bearer` header (immune to CSRF)
- Use Django's CSRF middleware for cookie-based auth
- Use `SameSite=Strict` cookie attribute
- Validate `Origin` header

**XSS:**
- Sanitize all user input before storing
- Escape output in templates
- Use strict CSP headers
- Store JWT in `httpOnly` cookie (not localStorage)

### How It Integrates with DRF

**CSRF Protection:**
```python
# settings.py — CSRF is active by default in Django
CSRF_COOKIE_HTTPONLY = True
CSRF_COOKIE_SECURE = True     # Only over HTTPS
CSRF_COOKIE_SAMESITE = 'Strict'

# For API views using SessionAuthentication, DRF enforces CSRF
# For JWT auth, CSRF is not needed (no cookies)
```

**XSS Prevention in Serializers:**
```python
import bleach
from rest_framework import serializers

class UserProfileSerializer(serializers.ModelSerializer):
    bio = serializers.CharField()

    def validate_bio(self, value):
        # Sanitize HTML to prevent XSS
        allowed_tags = ['b', 'i', 'em', 'strong']
        return bleach.clean(value, tags=allowed_tags, strip=True)

    class Meta:
        model = UserProfile
        fields = ['username', 'bio']
```

**CSP Header to mitigate XSS impact:**
```python
# settings.py with django-csp
CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'",)  # Block inline scripts
CSP_OBJECT_SRC = ("'none'",)  # Block plugins
```

---

## 3.3 API Abuse and Business Logic Attacks

### Simple Definition
**Business logic attacks** exploit flaws in how your API is supposed to work — not technical bugs, but flaws in the rules of your application.

> Example: A coupon code meant for single use — an attacker uses it 1000 times by sending parallel requests.

### Types of Business Logic Attacks
- **Price manipulation** — modify item price in request
- **Coupon abuse** — use coupon multiple times
- **Race conditions** — exploit time gap between check and action
- **Workflow bypass** — skip required steps (pay → ship without paying)
- **Quantity manipulation** — order negative quantities for refunds

### Why It Is Used (Understanding to Prevent)
- These can cause real financial damage
- Often missed by automated scanners
- Requires manual security review

### Prevention Techniques
- Server-side price validation (never trust client-side prices)
- Idempotency keys for one-time actions
- Database-level constraints
- Transaction locks for race conditions

### How It Integrates with DRF

**Race condition prevention with database lock:**
```python
from django.db import transaction
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Coupon

class ApplyCouponView(APIView):
    def post(self, request):
        coupon_code = request.data.get('code')

        with transaction.atomic():
            try:
                # Lock the row to prevent race conditions
                coupon = Coupon.objects.select_for_update().get(
                    code=coupon_code,
                    is_used=False
                )
                coupon.is_used = True
                coupon.used_by = request.user
                coupon.save()
                return Response({'discount': coupon.discount_amount})
            except Coupon.DoesNotExist:
                return Response(
                    {'error': 'Coupon invalid or already used'},
                    status=status.HTTP_400_BAD_REQUEST
                )
```

**Server-side price validation:**
```python
class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ['product', 'quantity']
        # Never include 'price' here — calculate server-side

    def create(self, validated_data):
        product = validated_data['product']
        quantity = validated_data['quantity']
        # Always use database price, never client-provided price
        validated_data['total_price'] = product.price * quantity
        return super().create(validated_data)
```

---

## 3.4 Mass Assignment Vulnerability

### Simple Definition
**Mass assignment** happens when an API blindly takes all user-provided fields and saves them to the database — including fields the user shouldn't be able to set.

> Example: User sends `{"username": "john", "is_admin": true}`. If your API accepts all fields, the user just made themselves an admin!

### Why It Is Used (Understanding to Prevent)
- Very common in APIs that auto-map request body to model fields
- Can lead to privilege escalation
- One of OWASP's most critical risks

### Prevention
- Use `fields` allowlist in serializers (not `exclude`)
- Use `read_only_fields` for sensitive fields
- Validate and restrict what fields can be written

### How It Integrates with DRF

**Vulnerable Code (DON'T DO THIS):**
```python
# ❌ BAD — exposes all fields
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'  # DANGEROUS!
```

**Fixed Code:**
```python
# ✅ GOOD — explicit allowlist
class UserUpdateSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['first_name', 'last_name', 'email']  # Only what user should change
        read_only_fields = ['is_admin', 'is_staff', 'date_joined', 'id']


# ✅ GOOD — separate serializers for read vs write
class UserReadSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'is_admin']


class UserWriteSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['first_name', 'last_name', 'email']  # No sensitive fields
```

**views.py:**
```python
class UserProfileView(generics.RetrieveUpdateAPIView):
    permission_classes = [IsAuthenticated]

    def get_serializer_class(self):
        if self.request.method == 'GET':
            return UserReadSerializer
        return UserWriteSerializer  # Limited write fields

    def get_object(self):
        return self.request.user  # Can only update own profile
```

---

## 3.5 Broken Object Level Authorization (BOLA)

### Simple Definition
**BOLA** (also called **IDOR — Insecure Direct Object Reference**) happens when a user can access another user's data just by changing an ID in the URL.

> Example: `GET /api/orders/123/` returns your order. But `GET /api/orders/456/` returns SOMEONE ELSE's order — because the API doesn't check if order 456 belongs to you!

### Why It Is Used (Understanding to Prevent)
- Most common API vulnerability (OWASP #1)
- Simple to exploit (just change numbers in URL)
- Can expose sensitive user data

### Prevention
- Always filter queries by the current user
- Never just look up by ID without ownership check
- Use UUIDs instead of sequential integers

### How It Integrates with DRF

**Vulnerable Code (DON'T DO THIS):**
```python
# ❌ BAD — anyone can access any order
class OrderViewSet(viewsets.ModelViewSet):
    queryset = Order.objects.all()  # Returns ALL orders
    serializer_class = OrderSerializer
```

**Fixed Code:**
```python
# ✅ GOOD — users can only see their own orders
class OrderViewSet(viewsets.ModelViewSet):
    serializer_class = OrderSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        # Filter by the current logged-in user
        return Order.objects.filter(user=self.request.user)

    def perform_create(self, serializer):
        # Always set owner to current user
        serializer.save(user=self.request.user)
```

**Use UUIDs to make IDs harder to guess:**
```python
import uuid

class Order(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    user = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    total = models.DecimalField(max_digits=10, decimal_places=2)
```

**Object-level permission check:**
```python
from rest_framework.permissions import BasePermission

class IsOwnerOfOrder(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.user == request.user
```

---

## 3.6 Broken Function Level Authorization (BFLA)

### Simple Definition
**BFLA** happens when a regular user can access admin-level functions — like deleting users, changing system settings, or viewing sensitive reports — just by knowing the URL.

> Example: `DELETE /api/admin/users/5/` should only work for admins, but a regular user can call it too because the API doesn't check roles.

### Prevention
- Check authorization for every function, not just data access
- Use permission classes on every view
- Don't hide admin endpoints without securing them

### How It Integrates with DRF

**Vulnerable Code:**
```python
# ❌ BAD — any authenticated user can delete any user
class UserDeleteView(APIView):
    permission_classes = [IsAuthenticated]  # Only checks if logged in

    def delete(self, request, pk):
        user = User.objects.get(pk=pk)
        user.delete()
        return Response(status=204)
```

**Fixed Code:**
```python
# ✅ GOOD — only admins can delete users
class UserDeleteView(APIView):
    permission_classes = [IsAuthenticated, IsAdminUser]

    def delete(self, request, pk):
        user = get_object_or_404(User, pk=pk)
        user.delete()
        return Response(status=204)


# ✅ GOOD — using get_permissions per action
class UserViewSet(viewsets.ModelViewSet):
    def get_permissions(self):
        if self.action == 'destroy':
            return [IsAuthenticated(), IsAdminUser()]
        elif self.action in ['update', 'partial_update']:
            return [IsAuthenticated(), IsOwnerOrAdmin()]
        return [IsAuthenticated()]
```

---

## 3.7 Rate Limiting Bypass Techniques

### Simple Definition
Attackers try to bypass rate limiting to send more requests than allowed. Common methods include changing IP addresses, rotating headers, or distributing attacks.

### Bypass Techniques (to defend against)
- **IP rotation** — change IP address for each request
- **Header spoofing** — fake `X-Forwarded-For` header
- **Distributed attack** — many IPs attacking simultaneously (DDoS)
- **Slow attack** — just below the rate limit threshold
- **Account rotation** — use many accounts

### Prevention
- Rate limit by user ID + IP combined
- Validate `X-Forwarded-For` — trust only from known proxies
- Use behavioral analysis (not just counts)
- Implement CAPTCHA for suspicious patterns
- Use cloud-level protection (Cloudflare, AWS Shield)

### How It Integrates with DRF

**Custom throttle using multiple identifiers:**
```python
from rest_framework.throttling import BaseThrottle
from django.core.cache import cache
import time

class UserAndIPThrottle(BaseThrottle):
    """Rate limit by user ID AND IP address combined."""
    rate = 100  # Max 100 requests
    period = 60  # Per 60 seconds

    def get_cache_key(self, request, view):
        # Combine user + IP for the key
        user_id = request.user.id if request.user.is_authenticated else 'anon'
        ip = self.get_ident(request)
        return f'throttle_{user_id}_{ip}'

    def allow_request(self, request, view):
        key = self.get_cache_key(request, view)
        history = cache.get(key, [])
        now = time.time()
        # Remove old requests
        history = [t for t in history if now - t < self.period]
        if len(history) >= self.rate:
            return False
        history.append(now)
        cache.set(key, history, self.period)
        return True

    def wait(self):
        return self.period
```

**Validate X-Forwarded-For from trusted proxies only:**
```python
# settings.py
TRUSTED_PROXIES = ['10.0.0.0/8', '172.16.0.0/12']  # Internal proxies only

# middleware.py
class ValidateIPMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Only trust X-Forwarded-For from known proxies
        # Otherwise use REMOTE_ADDR
        real_ip = request.META.get('REMOTE_ADDR')
        request.META['TRUE_CLIENT_IP'] = real_ip
        return self.get_response(request)
```

---

## 3.8 Data Exfiltration via APIs

### Simple Definition
**Data exfiltration** means an attacker extracts large amounts of sensitive data from your API — either through automation, exploiting missing auth, or through subtle API responses.

### Common Techniques
- **Scraping** — automated requests to collect all data
- **Over-fetching** — API returns more data than needed
- **Batch endpoints** — downloading thousands of records at once
- **Side-channel leakage** — error messages reveal existence of data

### Prevention
- Pagination — never return all records at once
- Field filtering — return only requested fields
- Rate limiting — slow down bulk extraction
- Monitor unusual patterns (large data downloads)

### How It Integrates with DRF

**Enforce pagination (prevent bulk export):**
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,  # Maximum 20 records per request
}
```

**Custom pagination with max limit:**
```python
from rest_framework.pagination import PageNumberPagination

class SecurePagination(PageNumberPagination):
    page_size = 20
    max_page_size = 100  # Absolute maximum — prevent huge requests

    def get_page_size(self, request):
        requested = super().get_page_size(request)
        return min(requested or self.page_size, self.max_page_size)
```

**Limit fields returned:**
```python
class UserListSerializer(serializers.ModelSerializer):
    """Minimal data for list endpoint — don't expose sensitive fields."""
    class Meta:
        model = User
        fields = ['id', 'username']  # Only public info


class UserDetailSerializer(serializers.ModelSerializer):
    """Full data only for authenticated owner."""
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'date_joined']
```

**views.py:**
```python
class UserViewSet(viewsets.ReadOnlyModelViewSet):
    pagination_class = SecurePagination
    permission_classes = [IsAuthenticated]

    def get_serializer_class(self):
        if self.action == 'list':
            return UserListSerializer  # Minimal data for lists
        return UserDetailSerializer   # More data for single record
```

---

## 3.9 Secure Pagination and Filtering Attacks

### Simple Definition
**Pagination attacks** happen when an attacker exploits your pagination system to enumerate all records or extract data. **Filtering attacks** use filter parameters to extract specific sensitive data or trigger heavy database queries.

### Types of Attacks
- **Enumeration via pagination** — loop through all pages to get all records
- **Filter injection** — craft filter parameters to access unauthorized data
- **Nested filter abuse** — very deep filters causing slow DB queries (DoS)
- **Ordering attacks** — order by sensitive fields to infer data

### Prevention
- Enforce max page size
- Validate and allowlist filter fields
- Limit query complexity
- Use cursor-based pagination for large datasets

### How It Integrates with DRF

**Allowlist filter fields:**
```python
from django_filters import rest_framework as filters
from django_filters.rest_framework import DjangoFilterBackend

class ProductFilter(filters.FilterSet):
    min_price = filters.NumberFilter(field_name='price', lookup_expr='gte')
    max_price = filters.NumberFilter(field_name='price', lookup_expr='lte')
    category = filters.CharFilter(field_name='category__name')
    # Only expose specific filters — NOT all fields

    class Meta:
        model = Product
        fields = ['category', 'min_price', 'max_price']  # Explicit allowlist
```

**Secure ordering:**
```python
from rest_framework.filters import OrderingFilter

class ProductViewSet(viewsets.ReadOnlyModelViewSet):
    filter_backends = [DjangoFilterBackend, OrderingFilter]
    filterset_class = ProductFilter
    ordering_fields = ['name', 'price', 'created_at']  # Only allowed fields
    ordering = ['name']  # Default ordering
```

**Cursor pagination for large datasets:**
```python
from rest_framework.pagination import CursorPagination

class CursorPaginationExample(CursorPagination):
    page_size = 20
    ordering = 'created_at'  # Must be unique and ordered field
    # Cursor-based: doesn't reveal total count or allow jumping to any page
```

---

## 3.10 API Versioning Security Risks

### Simple Definition
When you release a new API version (v2), the old version (v1) often stays running. **API versioning security risks** happen when old, insecure versions remain accessible and attackers use them to bypass security added in newer versions.

### Risks
- Old versions with known vulnerabilities still accessible
- Security fixes in v2 don't apply to v1
- Different auth requirements between versions
- Forgotten test/staging endpoints

### Prevention
- Retire old API versions with a clear timeline
- Apply security patches to ALL versions
- Use the same auth across all versions
- Audit and inventory all API versions

### How It Integrates with DRF

**URL-based versioning:**
```python
# urls.py
urlpatterns = [
    path('api/v1/', include('myapp.urls_v1')),
    path('api/v2/', include('myapp.urls_v2')),
]
```

**Deprecate old version with warning header:**
```python
class DeprecatedVersionMiddleware:
    """Add deprecation warning to old API version responses."""

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        if request.path.startswith('/api/v1/'):
            response['Deprecation'] = 'true'
            response['Sunset'] = 'Sat, 01 Jan 2026 00:00:00 GMT'
            response['Link'] = '</api/v2/>; rel="successor-version"'
        return response
```

**DRF built-in versioning:**
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION': 'v2',
    'ALLOWED_VERSIONS': ['v2'],  # Only allow current version
    'VERSION_PARAM': 'version',
}
```

---

# Level 4: Expert and Real-World Security

## 4.1 Zero Trust Architecture for APIs

### Simple Definition
**Zero Trust** means "never trust, always verify." Even if a request comes from inside your own network, you still verify identity and authorization. No one gets automatic trust just because of where they are.

> Old model: "You're inside the firewall, so you're trusted."
> Zero Trust: "Doesn't matter where you are. Prove who you are, every time."

### Principles
- Verify every request (authentication required always)
- Least privilege access
- Assume breach — design as if attackers are already inside
- Micro-segmentation — each service only talks to what it needs

### How It Integrates with DRF

**Enforce authentication on every endpoint:**
```python
# settings.py — No endpoint is public by default
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}
```

**Service-to-service auth with JWT:**
```python
# Each internal service has its own JWT for calling other services
class ServiceAuthMiddleware:
    """Validate JWT from internal services."""

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        service_token = request.headers.get('X-Service-Token')
        if service_token:
            # Validate the service's JWT
            request.calling_service = self.validate_service_token(service_token)
        return self.get_response(request)

    def validate_service_token(self, token):
        # Verify signature and claims
        import jwt
        payload = jwt.decode(token, settings.SERVICE_SECRET, algorithms=['HS256'])
        return payload.get('service_name')
```

---

## 4.2 Mutual TLS (mTLS)

### Simple Definition
In regular TLS, only the **server** presents a certificate. In **Mutual TLS (mTLS)**, BOTH the client AND server present certificates to verify each other.

> Regular TLS: You verify the bank's website is real.
> mTLS: The bank ALSO verifies that YOU are a legitimate client application.

### Why It Is Used
- Service-to-service authentication (microservices)
- High-security environments (banking, healthcare)
- Prevents unauthorized clients from connecting
- No need for API keys if using mTLS

### Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Strong mutual identity | Complex certificate management |
| Replaces API keys | Certificate rotation needed |
| Network-level security | Hard to implement for browsers |

### How It Integrates with DRF

**Django/DRF behind NGINX with mTLS:**

`nginx.conf`:
```nginx
server {
    listen 443 ssl;

    ssl_certificate /etc/ssl/server.crt;
    ssl_certificate_key /etc/ssl/server.key;

    # Require client certificate
    ssl_client_certificate /etc/ssl/ca.crt;
    ssl_verify_client on;

    location /api/ {
        # Pass client cert info to Django
        proxy_set_header X-SSL-Client-Verify $ssl_client_verify;
        proxy_set_header X-SSL-Client-DN $ssl_client_s_dn;
        proxy_pass http://django:8000;
    }
}
```

**DRF — read client cert from header:**
```python
class MTLSAuthentication(BaseAuthentication):
    """Authenticate clients using mTLS certificate info from NGINX."""

    def authenticate(self, request):
        client_verify = request.META.get('HTTP_X_SSL_CLIENT_VERIFY')
        client_dn = request.META.get('HTTP_X_SSL_CLIENT_DN')

        if client_verify != 'SUCCESS':
            return None

        # Extract service name from cert DN
        # Example DN: CN=payment-service,O=MyCompany
        service_name = self.extract_cn(client_dn)
        user = self.get_or_create_service_user(service_name)
        return (user, None)

    def extract_cn(self, dn):
        for part in dn.split(','):
            if part.strip().startswith('CN='):
                return part.strip()[3:]
        return None
```

---

## 4.3 HMAC Signatures

### Simple Definition
**HMAC (Hash-based Message Authentication Code)** is a way to sign API requests using a shared secret key. The client creates a signature using the secret + request data. The server recreates the signature and compares — if they match, the request is authentic and unchanged.

> Used by AWS APIs, Stripe webhooks, and many payment systems.

### Why It Is Used
- Proves request came from a legitimate client
- Proves request wasn't modified in transit
- Stateless — no tokens to manage
- Perfect for webhooks and machine-to-machine APIs

### How HMAC Works
```
1. Client: signature = HMAC-SHA256(secret_key, method + path + timestamp + body)
2. Client: sends request with signature in header
3. Server: recreates same signature
4. Server: compares → if match, request is valid
```

### How It Integrates with DRF

**Client (creating HMAC signature):**
```python
import hmac
import hashlib
import time

def create_hmac_signature(secret_key, method, path, body, timestamp):
    message = f'{method}\n{path}\n{timestamp}\n{body}'
    signature = hmac.new(
        secret_key.encode(),
        message.encode(),
        hashlib.sha256
    ).hexdigest()
    return signature

# When making a request:
timestamp = str(int(time.time()))
body = '{"amount": 100}'
signature = create_hmac_signature('my_secret', 'POST', '/api/payment/', body, timestamp)
# Send: X-Signature: <signature>, X-Timestamp: <timestamp>
```

**Server (DRF — verifying HMAC):**
```python
import hmac
import hashlib
import time

class HMACAuthentication(BaseAuthentication):
    def authenticate(self, request):
        signature = request.headers.get('X-Signature')
        timestamp = request.headers.get('X-Timestamp')

        if not signature or not timestamp:
            return None

        # Prevent replay attacks — reject requests older than 5 minutes
        if abs(time.time() - int(timestamp)) > 300:
            raise AuthenticationFailed('Request expired')

        # Recreate expected signature
        body = request.body.decode('utf-8')
        message = f'{request.method}\n{request.path}\n{timestamp}\n{body}'
        expected = hmac.new(
            settings.HMAC_SECRET.encode(),
            message.encode(),
            hashlib.sha256
        ).hexdigest()

        # Constant-time comparison to prevent timing attacks
        if not hmac.compare_digest(signature, expected):
            raise AuthenticationFailed('Invalid signature')

        return (AnonymousUser(), None)
```

---

## 4.4 API Gateway and WAF Integration

### Simple Definition
A **WAF (Web Application Firewall)** is a security layer that inspects HTTP traffic and blocks malicious requests before they reach your API. Combined with an API Gateway, it provides comprehensive protection.

### WAF vs API Gateway

| Feature | API Gateway | WAF |
|---|---|---|
| Routing | ✅ | ❌ |
| Rate limiting | ✅ | ✅ |
| Auth | ✅ | ❌ |
| SQL injection protection | ❌ | ✅ |
| XSS protection | ❌ | ✅ |
| Bot detection | Partial | ✅ |

### How It Integrates with DRF

**Django with AWS WAF via API Gateway:**
```python
# settings.py — Trust API Gateway headers
REST_FRAMEWORK = {
    # API Gateway validates JWT before forwarding
    # DRF still validates for defense-in-depth
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ]
}

# Trust forwarded headers from gateway
USE_X_FORWARDED_HOST = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
```

**Middleware to check WAF headers:**
```python
class WAFMiddleware:
    """Process security headers added by WAF."""

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # AWS WAF passes this header if request was blocked
        waf_result = request.META.get('HTTP_X_AMZ_WAF_RESULT')
        if waf_result == 'BLOCK':
            from django.http import HttpResponseForbidden
            return HttpResponseForbidden('Blocked by WAF')
        return self.get_response(request)
```

---

## 4.5 DDoS Protection Strategies

### Simple Definition
**DDoS (Distributed Denial of Service)** is when thousands of machines flood your API with requests, making it unavailable to real users. Protection involves detecting and blocking this traffic.

### Layers of Protection

| Layer | Solution |
|---|---|
| Network level | Cloud provider DDoS protection (Cloudflare, AWS Shield) |
| CDN level | Absorb traffic before it hits your servers |
| Application level | Rate limiting, CAPTCHA |
| DNS level | Anycast DNS routing |

### How It Integrates with DRF

**Multi-layer rate limiting:**
```python
from rest_framework.throttling import AnonRateThrottle, UserRateThrottle
from django.core.cache import cache

class BurstThrottle(AnonRateThrottle):
    """Allow short bursts but limit sustained traffic."""
    scope = 'burst'

class SustainedThrottle(AnonRateThrottle):
    """Long-term rate limit."""
    scope = 'sustained'
```

**settings.py:**
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'myapp.throttling.BurstThrottle',
        'myapp.throttling.SustainedThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'burst': '60/minute',       # Short burst limit
        'sustained': '1000/hour',   # Long-term limit
    }
}
```

**IP blocklist middleware:**
```python
BLOCKED_IPS = set()  # Load from Redis or database

class IPBlocklistMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        ip = request.META.get('REMOTE_ADDR')
        if ip in BLOCKED_IPS:
            from django.http import HttpResponseForbidden
            return HttpResponseForbidden()
        return self.get_response(request)
```

---

## 4.6 Secrets Management

### Simple Definition
**Secrets management** means securely storing and accessing sensitive information like API keys, database passwords, and private keys — not hardcoding them in your code.

### Tools
- **HashiCorp Vault** — enterprise secrets manager
- **AWS KMS/Secrets Manager** — cloud-managed secrets
- **Azure Key Vault** — Microsoft's secrets service
- **python-decouple / python-dotenv** — environment variable management

### Tips and Tricks
- Never put secrets in code or version control
- Rotate secrets regularly
- Use different secrets per environment (dev/staging/prod)
- Audit secret access logs

### How It Integrates with DRF

**Using python-decouple (.env files):**
```bash
pip install python-decouple
```

**.env file (never commit this!):**
```
SECRET_KEY=your-very-long-random-secret-key
DATABASE_URL=postgres://user:pass@localhost/dbname
JWT_SECRET=another-random-secret
REDIS_URL=redis://localhost:6379
```

**settings.py:**
```python
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
DATABASE_URL = config('DATABASE_URL')

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

**Using AWS Secrets Manager:**
```python
import boto3
import json

def get_secret(secret_name):
    """Fetch secret from AWS Secrets Manager."""
    client = boto3.client('secretsmanager', region_name='us-east-1')
    response = client.get_secret_value(SecretId=secret_name)
    return json.loads(response['SecretString'])

# In settings.py
secrets = get_secret('myapp/production')
SECRET_KEY = secrets['django_secret_key']
DB_PASSWORD = secrets['db_password']
```

**HashiCorp Vault:**
```python
import hvac

def get_vault_secret(path):
    client = hvac.Client(url='https://vault.example.com', token='your-token')
    response = client.secrets.kv.read_secret_version(path=path)
    return response['data']['data']

secrets = get_vault_secret('myapp/database')
DB_PASSWORD = secrets['password']
```

---

## 4.7 API Threat Modeling

### Simple Definition
**Threat modeling** is the process of identifying all possible threats to your API before they happen — and planning how to defend against them.

### STRIDE Framework

| Threat | Meaning | Example |
|---|---|---|
| **S**poofing | Pretending to be someone else | Fake tokens |
| **T**ampering | Modifying data in transit | Changing request body |
| **R**epudiation | Denying you did something | No audit logs |
| **I**nformation Disclosure | Leaking sensitive data | Error messages with stack traces |
| **D**enial of Service | Making API unavailable | DDoS |
| **E**levation of Privilege | Getting more access than allowed | BOLA, BFLA |

### Process
1. Map all API endpoints and data flows
2. Identify assets (what's valuable?)
3. Identify threats using STRIDE
4. Rate each threat (likelihood × impact)
5. Plan mitigations
6. Implement and verify

### How It Integrates with DRF

**Threat model checklist for DRF APIs:**
```python
"""
STRIDE Checklist for DRF:

S - Spoofing:
  ✅ JWT authentication on all endpoints
  ✅ Strong password validation
  ✅ Rate limit login attempts

T - Tampering:
  ✅ Input validation in serializers
  ✅ HTTPS (TLS) for all traffic
  ✅ HMAC signatures for webhooks

R - Repudiation:
  ✅ Log all requests with user ID and timestamp
  ✅ Audit trail for data changes

I - Information Disclosure:
  ✅ DEBUG=False in production
  ✅ Generic error messages (no stack traces)
  ✅ Filter sensitive fields from serializers

D - Denial of Service:
  ✅ Rate limiting (throttling)
  ✅ Pagination to limit response size
  ✅ Query complexity limits

E - Elevation of Privilege:
  ✅ RBAC with permission classes
  ✅ Object-level authorization (BOLA prevention)
  ✅ Separate serializers for write operations
"""
```

**Audit logging middleware:**
```python
import logging

logger = logging.getLogger('api.audit')

class AuditLogMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        logger.info({
            'user': str(request.user),
            'method': request.method,
            'path': request.path,
            'status': response.status_code,
            'ip': request.META.get('REMOTE_ADDR'),
        })
        return response
```

---

## 4.8 Schema Validation

### Simple Definition
**Schema validation** means defining the exact structure your API expects — and rejecting anything that doesn't match. Tools like JSON Schema and OpenAPI help you define and enforce these rules.

### Tools
- **JSON Schema** — defines structure of JSON data
- **OpenAPI (Swagger)** — describes your entire API
- **drf-spectacular** — auto-generates OpenAPI spec from DRF code

### Why It Is Used
- Rejects malformed requests early
- Auto-generates API documentation
- Powers API testing and mocking
- Prevents unexpected data shapes

### How It Integrates with DRF

**Install drf-spectacular:**
```bash
pip install drf-spectacular
```

**settings.py:**
```python
INSTALLED_APPS = ['drf_spectacular']

REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'My API',
    'DESCRIPTION': 'API documentation',
    'VERSION': '1.0.0',
}
```

**urls.py:**
```python
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView

urlpatterns = [
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema')),
]
```

**Annotate views for better schema:**
```python
from drf_spectacular.utils import extend_schema, OpenApiParameter
from drf_spectacular.types import OpenApiTypes

class ProductListView(generics.ListAPIView):
    serializer_class = ProductSerializer

    @extend_schema(
        parameters=[
            OpenApiParameter('category', OpenApiTypes.STR, description='Filter by category'),
            OpenApiParameter('max_price', OpenApiTypes.FLOAT, description='Max price filter'),
        ],
        responses={200: ProductSerializer(many=True)},
        description='List all products with optional filters',
    )
    def get(self, request, *args, **kwargs):
        return super().get(request, *args, **kwargs)
```

**JSON Schema validation in serializer:**
```python
import jsonschema
from rest_framework import serializers

METADATA_SCHEMA = {
    "type": "object",
    "properties": {
        "color": {"type": "string"},
        "size": {"type": "string", "enum": ["S", "M", "L", "XL"]},
    },
    "required": ["color", "size"]
}

class ProductSerializer(serializers.ModelSerializer):
    metadata = serializers.JSONField()

    def validate_metadata(self, value):
        try:
            jsonschema.validate(value, METADATA_SCHEMA)
        except jsonschema.ValidationError as e:
            raise serializers.ValidationError(str(e.message))
        return value
```

---

## 4.9 GraphQL Security

### Simple Definition
**GraphQL** is a query language for APIs that lets clients request exactly the data they want. While powerful, it introduces unique security risks not present in REST APIs.

### Security Risks
- **Introspection abuse** — attackers learn your entire schema
- **Depth attacks** — deeply nested queries crash your server
- **Batch attacks** — send 1000 mutations in one request
- **Field suggestion attacks** — GraphQL suggests field names when you mistype
- **Circular query attacks** — infinitely recursive queries

### Prevention
- Disable introspection in production
- Limit query depth
- Limit query complexity
- Rate limit by operation
- Use persisted queries in production

### How It Integrates with DRF (Graphene-Django)

**Install:**
```bash
pip install graphene-django
```

**settings.py:**
```python
INSTALLED_APPS = ['graphene_django']

GRAPHENE = {
    'SCHEMA': 'myapp.schema.schema',
}
```

**Disable introspection in production:**
```python
# schema.py
import graphene
from graphene_django.views import GraphQLView
from django.conf import settings

class NoIntrospectionMiddleware:
    """Disable GraphQL introspection in production."""

    def resolve(self, next, root, info, **args):
        if not settings.DEBUG:
            if info.field_name in ('__schema', '__type', '__typename'):
                from graphql import GraphQLError
                raise GraphQLError('Introspection is disabled')
        return next(root, info, **args)
```

**Limit query depth:**
```bash
pip install graphene-django-query-depth-limit
```
```python
from graphql_depth_limit import depth_limit_validator

GRAPHENE = {
    'SCHEMA': 'myapp.schema.schema',
    'VALIDATION_RULES': [depth_limit_validator(5)],  # Max 5 levels deep
}
```

**Authentication in GraphQL:**
```python
from graphene_django.views import GraphQLView
from rest_framework_simplejwt.authentication import JWTAuthentication
from django.utils.decorators import method_decorator
from django.views.decorators.csrf import csrf_exempt

class AuthenticatedGraphQLView(GraphQLView):
    def dispatch(self, request, *args, **kwargs):
        authenticator = JWTAuthentication()
        try:
            result = authenticator.authenticate(request)
            if result:
                request.user = result[0]
        except Exception:
            pass
        return super().dispatch(request, *args, **kwargs)

# urls.py
urlpatterns = [
    path('graphql/', csrf_exempt(AuthenticatedGraphQLView.as_view(graphiql=settings.DEBUG))),
]
```

---

## 4.10 gRPC Security Considerations

### Simple Definition
**gRPC** is a high-performance API framework by Google that uses Protocol Buffers (binary format) instead of JSON. It's faster than REST but has its own security considerations.

### Security Considerations
- **TLS required** — gRPC should always use TLS
- **mTLS support** — excellent for service-to-service auth
- **Authentication** — use metadata (like HTTP headers) for tokens
- **Authorization** — implement in interceptors
- **Input validation** — Protobuf provides type validation
- **Reflection service** — disable in production (like GraphQL introspection)

### Key Security Features
- Built-in TLS/mTLS support
- Channel-level and call-level credentials
- Interceptors for auth (like middleware)
- Binary protocol — harder to tamper manually

### How It Integrates with Django (grpcio + DRF)

**Install:**
```bash
pip install grpcio grpcio-tools
```

**Secure gRPC server with TLS:**
```python
import grpc
from concurrent import futures

def create_secure_server():
    # Load certificates
    with open('server.key', 'rb') as f:
        private_key = f.read()
    with open('server.crt', 'rb') as f:
        certificate_chain = f.read()

    # Create SSL credentials
    server_credentials = grpc.ssl_server_credentials([(private_key, certificate_chain)])

    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))

    # For mTLS, require client certificate:
    # server_credentials = grpc.ssl_server_credentials(
    #     [(private_key, certificate_chain)],
    #     root_certificates=ca_cert,
    #     require_client_auth=True
    # )

    server.add_secure_port('[::]:50051', server_credentials)
    return server
```

**Auth interceptor (like middleware):**
```python
import grpc
from rest_framework_simplejwt.tokens import AccessToken

class AuthInterceptor(grpc.ServerInterceptor):
    """Validate JWT token from gRPC metadata."""

    def intercept_service(self, continuation, handler_call_details):
        metadata = dict(handler_call_details.invocation_metadata)
        token = metadata.get('authorization', '').replace('Bearer ', '')

        if not token:
            def abort(ignored_request, context):
                context.abort(grpc.StatusCode.UNAUTHENTICATED, 'Missing token')
            return grpc.unary_unary_rpc_method_handler(abort)

        try:
            AccessToken(token)  # Validate JWT
        except Exception:
            def abort(ignored_request, context):
                context.abort(grpc.StatusCode.UNAUTHENTICATED, 'Invalid token')
            return grpc.unary_unary_rpc_method_handler(abort)

        return continuation(handler_call_details)
```

**Disable reflection in production:**
```python
from grpc_reflection.v1alpha import reflection

def add_services(server):
    # Add your service
    add_ProductServiceServicer_to_server(ProductService(), server)

    # Only enable reflection in development
    if settings.DEBUG:
        SERVICE_NAMES = (
            product_pb2.DESCRIPTOR.services_by_name['ProductService'].full_name,
            reflection.SERVICE_NAME,
        )
        reflection.enable_server_reflection(SERVICE_NAMES, server)
    # In production: don't add reflection — attackers won't know your schema
```

---

# Quick Reference Checklists

## DRF Security Checklist

```
Authentication & Authorization:
  ☐ IsAuthenticated on all endpoints
  ☐ JWT with short expiry (15 min access, 7 day refresh)
  ☐ Refresh token rotation and blacklisting
  ☐ Object-level authorization (filter by owner)
  ☐ Role-based permissions on admin functions

Input Security:
  ☐ Serializer validation on all inputs
  ☐ Explicit fields list (no __all__)
  ☐ read_only_fields for sensitive fields
  ☐ HTML sanitization (bleach) for rich text
  ☐ JSON schema validation where needed

Transport Security:
  ☐ HTTPS enforced (SECURE_SSL_REDIRECT=True)
  ☐ HSTS enabled
  ☐ Secure cookies (SESSION_COOKIE_SECURE=True)

Rate Limiting:
  ☐ Throttling on all endpoints
  ☐ Stricter limits on auth endpoints
  ☐ Pagination enforced (max page size)

Headers:
  ☐ CORS configured (no wildcard in production)
  ☐ X-Frame-Options: DENY
  ☐ X-Content-Type-Options: nosniff
  ☐ Content-Security-Policy
  ☐ Referrer-Policy

Secrets & Config:
  ☐ DEBUG=False in production
  ☐ Secrets in environment variables (not code)
  ☐ No stack traces in error responses
  ☐ Generic error messages

Logging:
  ☐ Audit log all requests
  ☐ Log authentication failures
  ☐ Log rate limit hits
  ☐ No sensitive data in logs
```

---

## OWASP API Top 10 — DRF Fix Reference

| OWASP Risk | DRF Fix |
|---|---|
| BOLA | `filter(user=request.user)` in `get_queryset` |
| Broken Auth | JWT + short expiry + blacklisting |
| Mass Assignment | Explicit `fields` list, `read_only_fields` |
| Rate Limiting | `DEFAULT_THROTTLE_CLASSES` |
| BFLA | `get_permissions()` per action |
| Business Logic | `select_for_update()`, server-side validation |
| SSRF | Validate URLs, use allowlist of allowed domains |
| Misconfiguration | `DEBUG=False`, proper CORS, remove test endpoints |
| Old Versions | Retire old API versions, `ALLOWED_VERSIONS` |
| Third-party Trust | Validate all external data with serializers |
```
