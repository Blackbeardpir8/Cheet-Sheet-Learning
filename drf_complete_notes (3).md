# 🐍 Django REST Framework — Complete Backend Developer Notes

> **Beginner-friendly, production-ready guide covering all 44 topics**

---

## 📑 Table of Contents

1. [HTTPS Encryption](#1-https-encryption)
2. [Rate Limiting](#2-rate-limiting)
3. [Data Encryption](#3-data-encryption)
4. [Throttle Login Attempts](#4-throttle-login-attempts)
5. [Security Headers](#5-security-headers)
6. [Token Expiration](#6-token-expiration)
7. [Access Control](#7-access-control)
8. [Sanitize Inputs](#8-sanitize-inputs)
9. [Secure Error Messages](#9-secure-error-messages)
10. [Logging and Auditing](#10-logging-and-auditing)
11. [API Versioning](#11-api-versioning)
12. [CORS Configuration](#12-cors-configuration)
13. [Secure Data Validation](#13-secure-data-validation)
14. [Security Testing](#14-security-testing)
15. [WebSockets](#15-websockets)
16. [Cron Jobs](#16-cron-jobs)
17. [API Documentation with Postman](#17-api-documentation-with-postman)
18. [Pagination and Its Types](#18-pagination-and-its-types)
19. [Idempotency](#19-idempotency)
20. [Which Methods Are Idempotent](#20-which-methods-are-idempotent)
21. [Redis in DRF](#21-redis-in-drf)
22. [Celery](#22-celery)
23. [Kafka](#23-kafka)
24. [Logs for Debugging](#24-logs-for-debugging)
25. [Making System Scalable](#25-making-system-scalable)
26. [Mixins and Its Types](#26-mixins-and-its-types)
27. [Searching and Fetching Data — Fastest Methods](#27-searching-and-fetching-data--fastest-methods)
28. [Trigram Similarity and Elasticsearch](#28-trigram-similarity-and-elasticsearch)
29. [Permissions](#29-permissions)
30. [Atomic Transactions](#30-atomic-transactions)
31. [Types of Views in DRF](#31-types-of-views-in-drf)
32. [Serializers — Complete Guide](#32-serializers--complete-guide)
33. [Views and Types in DRF](#33-views-and-types-in-drf)
34. [Exposing Choice Fields to Frontend](#34-exposing-choice-fields-to-frontend)
35. [Other Important Backend Topics](#35-other-important-backend-topics)
36. [Database Indexing](#36-database-indexing)
37. [Query Optimization](#37-query-optimization)
38. [Load Balancing](#38-load-balancing)
39. [Design Patterns](#39-design-patterns)
40. [Clean Architecture](#40-clean-architecture)
41. [OAuth2](#41-oauth2)
42. [OpenAPI / Swagger](#42-openapi--swagger)
43. [Testing — Unit and Integration](#43-testing--unit-and-integration)
44. [SOLID Principles](#44-solid-principles)

---

## 1. HTTPS Encryption

### Simple Definition
HTTPS = HTTP + SSL/TLS. It encrypts the data traveling between the browser and your server so no one can read it in between.

### Why It Is Used
- Protects passwords, tokens, personal data from being stolen
- Required for modern browsers and APIs
- Builds user trust

### Types
- **SSL (old)** — deprecated
- **TLS 1.2** — still used
- **TLS 1.3** — current standard, faster and more secure

### Advantages
- Data is encrypted in transit
- Protects against man-in-the-middle attacks
- Required for HTTP/2

### Disadvantages
- Needs SSL certificate (free via Let's Encrypt)
- Slight overhead (negligible with TLS 1.3)

### Best Practices
```python
# settings.py
SECURE_SSL_REDIRECT = True          # Redirect HTTP → HTTPS
SESSION_COOKIE_SECURE = True        # Cookies only over HTTPS
CSRF_COOKIE_SECURE = True           # CSRF cookie only over HTTPS
SECURE_HSTS_SECONDS = 31536000      # 1 year HSTS
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
```

### Tips
- Use **Let's Encrypt** (free) or Cloudflare for SSL certs
- Always redirect HTTP to HTTPS
- Set HSTS to prevent downgrade attacks

---

## 2. Rate Limiting

### Simple Definition
Rate limiting = "You can only make X requests per minute." It stops people from abusing your API.

### Why It Is Used
- Prevents DDoS attacks
- Stops brute-force attacks
- Protects server resources

### Types in DRF
- **AnonRateThrottle** — limits unauthenticated users
- **UserRateThrottle** — limits per logged-in user
- **ScopedRateThrottle** — custom rates per view

### Setup
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day',
    }
}
```

### Per-View Throttling
```python
from rest_framework.throttling import UserRateThrottle
from rest_framework.views import APIView

class LoginThrottle(UserRateThrottle):
    rate = '5/min'

class LoginView(APIView):
    throttle_classes = [LoginThrottle]

    def post(self, request):
        # login logic
        pass
```

### Best Practices
- Use Redis as cache backend for distributed throttling
- Return `Retry-After` header with throttle errors
- Use different rates for different endpoints

---

## 3. Data Encryption

### Simple Definition
Scrambling data so only authorized parties can read it. Even if someone steals the database, they can't read the data.

### Types
- **Symmetric** — same key to encrypt and decrypt (AES)
- **Asymmetric** — public key encrypts, private key decrypts (RSA)
- **Hashing** — one-way, can't decrypt (bcrypt, SHA-256) — used for passwords

### In Django
```python
# For passwords — Django handles automatically with bcrypt
AUTH_PASSWORD_VALIDATORS = [...]

# For sensitive fields — use django-encrypted-model-fields
pip install django-encrypted-model-fields

# settings.py
FIELD_ENCRYPTION_KEY = 'your-32-char-secret-key-here'

# models.py
from encrypted_model_fields.fields import EncryptedCharField

class UserProfile(models.Model):
    ssn = EncryptedCharField(max_length=20)  # encrypted in DB
```

### Hashing Passwords Manually
```python
from django.contrib.auth.hashers import make_password, check_password

hashed = make_password('mypassword123')
is_valid = check_password('mypassword123', hashed)  # True
```

### Best Practices
- Never store plain text passwords
- Use `bcrypt` or `argon2` for password hashing
- Rotate encryption keys periodically
- Encrypt PII fields (SSN, phone, bank data)

---

## 4. Throttle Login Attempts

### Simple Definition
After N failed login attempts, block or slow down further attempts. Stops brute-force password attacks.

### Implementation
```python
# throttles.py
from rest_framework.throttling import AnonRateThrottle

class LoginRateThrottle(AnonRateThrottle):
    rate = '5/min'
    scope = 'login'

# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_RATES': {
        'login': '5/min',
    }
}

# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from .throttles import LoginRateThrottle

class LoginView(APIView):
    throttle_classes = [LoginRateThrottle]

    def post(self, request):
        username = request.data.get('username')
        password = request.data.get('password')
        user = authenticate(username=username, password=password)
        if user:
            token, _ = Token.objects.get_or_create(user=user)
            return Response({'token': token.key})
        return Response({'error': 'Invalid credentials'}, status=400)
```

### Advanced: Track Failed Attempts with Redis
```python
import redis
r = redis.Redis()

def check_login_attempts(username):
    key = f'login_attempts:{username}'
    attempts = r.get(key)
    if attempts and int(attempts) >= 5:
        return False  # blocked
    r.incr(key)
    r.expire(key, 300)  # reset after 5 mins
    return True
```

### Best Practices
- Lock account after 10 failed attempts
- Use CAPTCHA after 3 failed attempts
- Log all failed login attempts
- Send alert email on suspicious activity

---

## 5. Security Headers

### Simple Definition
HTTP response headers that tell the browser how to behave securely. Like safety rules for the browser.

### Common Headers
| Header | Purpose |
|--------|---------|
| `X-Content-Type-Options` | No MIME sniffing |
| `X-Frame-Options` | Prevent clickjacking |
| `X-XSS-Protection` | XSS filter |
| `Content-Security-Policy` | Control resource loading |
| `Strict-Transport-Security` | Force HTTPS |

### Setup in Django
```python
# settings.py
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_TYPE_NOSNIFF = True
X_FRAME_OPTIONS = 'DENY'           # Prevent iframes
SECURE_HSTS_SECONDS = 31536000

# For advanced CSP, use django-csp
pip install django-csp

# settings.py
CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'", "cdn.example.com")
CSP_STYLE_SRC = ("'self'", "'unsafe-inline'")
```

### Using django-axes for extra security
```python
pip install django-axes

# settings.py
INSTALLED_APPS += ['axes']
AXES_FAILURE_LIMIT = 5
AXES_COOLOFF_TIME = 1  # hours
```

### Best Practices
- Use `mozilla observatory` to scan your headers
- Never expose server version in headers
- Use `X-Frame-Options: DENY` unless you need iframes

---

## 6. Token Expiration

### Simple Definition
Tokens are like temporary keys. After some time, they expire and the user must log in again. This limits damage if a token is stolen.

### Types of Tokens
- **Simple Token (DRF built-in)** — no expiry by default
- **JWT (JSON Web Token)** — has built-in expiry
- **Refresh Token** — used to get a new access token

### JWT Setup with SimpleJWT
```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
from datetime import timedelta

INSTALLED_APPS += ['rest_framework_simplejwt']

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view()),
    path('api/token/refresh/', TokenRefreshView.as_view()),
]
```

### Flow
```
1. POST /api/token/ → {access, refresh}
2. Use access token in header: Authorization: Bearer <token>
3. After 30 min → access token expires
4. POST /api/token/refresh/ with refresh token → new access token
5. After 7 days → refresh token expires → must login again
```

### Best Practices
- Short access token (15-30 min)
- Longer refresh token (7-30 days)
- Blacklist refresh tokens on logout
- Store tokens in HttpOnly cookies (not localStorage)

---

## 7. Access Control

### Simple Definition
Deciding who can do what. "Admin can delete, normal user can only view."

### Types
- **Authentication** — who are you? (login)
- **Authorization / Permissions** — what can you do?
- **RBAC** — Role-Based Access Control
- **Object-level permissions** — can you access THIS object?

### DRF Permission Classes
```python
# views.py
from rest_framework.permissions import IsAuthenticated, IsAdminUser, AllowAny

class PostView(APIView):
    permission_classes = [IsAuthenticated]  # Must be logged in

class AdminView(APIView):
    permission_classes = [IsAdminUser]  # Must be admin

class PublicView(APIView):
    permission_classes = [AllowAny]  # Open to everyone
```

### Custom Permission
```python
# permissions.py
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user

# views.py
class PostDetailView(RetrieveUpdateDestroyAPIView):
    permission_classes = [IsAuthenticated, IsOwner]
```

### Best Practices
- Apply permissions at view level, not just URL level
- Use object-level permissions for owned resources
- Log all permission denials
- Follow least privilege principle

---

## 8. Sanitize Inputs

### Simple Definition
Cleaning user inputs before using them. Prevents SQL injection, XSS, and other attacks.

### Why It Is Used
- Users can send malicious data: `<script>alert('hack')</script>`
- SQL injection: `'; DROP TABLE users; --`
- DRF serializers handle much of this automatically

### DRF Built-in Sanitization
```python
# Serializers automatically validate types
class UserSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)  # strips extra
    age = serializers.IntegerField(min_value=0, max_value=150)
    email = serializers.EmailField()

# validate_<field> for custom validation
def validate_name(self, value):
    if '<script>' in value.lower():
        raise serializers.ValidationError("Invalid input")
    return value.strip()
```

### Bleach for HTML Sanitization
```python
pip install bleach

import bleach
clean = bleach.clean('<script>alert("xss")</script> Hello')
# Output: &lt;script&gt;alert("xss")&lt;/script&gt; Hello
```

### Best Practices
- Never use raw SQL with user input
- Use Django ORM (parameterized queries) always
- Validate on both frontend AND backend
- Strip whitespace, normalize encoding

---

## 9. Secure Error Messages

### Simple Definition
Don't tell attackers what went wrong. "Invalid credentials" instead of "User not found" or "Wrong password."

### Bad vs Good
```python
# BAD — reveals too much
if not user:
    return Response({'error': 'User with this email does not exist'})
if not check_password(password, user.password):
    return Response({'error': 'Password is incorrect'})

# GOOD — vague on purpose
return Response({'error': 'Invalid credentials'}, status=401)
```

### Django Settings
```python
# settings.py
DEBUG = False  # NEVER True in production!
# DEBUG=True shows full stack traces to everyone — dangerous!

# Custom 404/500 handlers
handler404 = 'myapp.views.custom_404'
handler500 = 'myapp.views.custom_500'
```

### Custom Error Handler
```python
# views.py
from rest_framework.views import exception_handler
from rest_framework.response import Response

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)
    if response is not None:
        response.data = {
            'error': response.data,
            'status_code': response.status_code
        }
    return response

# settings.py
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'myapp.views.custom_exception_handler'
}
```

### Best Practices
- `DEBUG = False` always in production
- Log full errors internally, show generic message to user
- Use Sentry for error monitoring

---

## 10. Logging and Auditing

### Simple Definition
Recording what happened, when, and by whom. Like a security camera for your application.

### Why It Is Used
- Debug issues in production
- Detect security breaches
- Compliance requirements
- Track user actions

### Django Logging Setup
```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'django.log',
            'formatter': 'verbose',
        },
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file', 'console'],
            'level': 'INFO',
            'propagate': True,
        },
        'myapp': {
            'handlers': ['file'],
            'level': 'DEBUG',
        },
    },
}
```

### Using Logger in Views
```python
import logging
logger = logging.getLogger('myapp')

class LoginView(APIView):
    def post(self, request):
        logger.info(f"Login attempt for {request.data.get('email')}")
        # ... logic
        logger.warning(f"Failed login for {request.data.get('email')}")
```

### Audit Log with Model
```python
class AuditLog(models.Model):
    user = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
    action = models.CharField(max_length=100)
    timestamp = models.DateTimeField(auto_now_add=True)
    ip_address = models.GenericIPAddressField()
    details = models.JSONField()
```

### Best Practices
- Log at appropriate levels (DEBUG, INFO, WARNING, ERROR, CRITICAL)
- Never log passwords or tokens
- Rotate log files (use `RotatingFileHandler`)
- Use structured logging (JSON format) for log aggregators
- Ship logs to Elasticsearch, Datadog, or CloudWatch

---

## 11. API Versioning

### Simple Definition
Keeping old API working while adding new features. Like releasing v1 and v2 of an app — old users keep using v1.

### Types in DRF
- **URL Versioning** — `/api/v1/users/` (most common)
- **Header Versioning** — `Accept: application/json; version=1.0`
- **Query Param Versioning** — `/api/users/?version=1`
- **Namespace Versioning** — URL namespaces

### URL Versioning Setup
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION': 'v1',
    'ALLOWED_VERSIONS': ['v1', 'v2'],
}

# urls.py
urlpatterns = [
    path('api/<version>/users/', UserListView.as_view()),
]

# views.py
class UserListView(APIView):
    def get(self, request, *args, **kwargs):
        if request.version == 'v2':
            # new behavior
            pass
        else:
            # v1 behavior
            pass
```

### Clean URL Structure
```python
# urls.py
from django.urls import path, include

v1_patterns = [path('users/', views.UserListV1.as_view())]
v2_patterns = [path('users/', views.UserListV2.as_view())]

urlpatterns = [
    path('api/v1/', include(v1_patterns)),
    path('api/v2/', include(v2_patterns)),
]
```

### Best Practices
- Use URL versioning for simplicity
- Maintain old versions for at least 6 months
- Document deprecation policy
- Version from day one — hard to add later

---

## 12. CORS Configuration

### Simple Definition
CORS = Cross-Origin Resource Sharing. It decides which websites can call your API. By default, browsers block requests from other domains.

### Example Problem
```
Frontend: https://myfrontend.com
Backend:  https://myapi.com

Browser blocks this by default → need CORS
```

### Setup
```bash
pip install django-cors-headers
```

```python
# settings.py
INSTALLED_APPS += ['corsheaders']

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # MUST be first
    'django.middleware.common.CommonMiddleware',
    # ... rest
]

# Allow specific origins
CORS_ALLOWED_ORIGINS = [
    "https://myfrontend.com",
    "http://localhost:3000",
]

# OR allow all (not recommended in production)
CORS_ALLOW_ALL_ORIGINS = True

# Allow cookies/auth headers
CORS_ALLOW_CREDENTIALS = True

# Allow specific headers
CORS_ALLOW_HEADERS = [
    'authorization',
    'content-type',
    'x-requested-with',
]
```

### Best Practices
- Never use `CORS_ALLOW_ALL_ORIGINS = True` in production
- Explicitly list allowed origins
- Allow credentials only when needed (JWT in headers is fine without it)
- Set `CORS_URLS_REGEX` to limit CORS to `/api/` only

---

## 13. Secure Data Validation

### Simple Definition
Checking that data is correct, safe, and makes sense before saving it to the database.

### DRF Validation Levels
1. **Field-level** — individual field validation
2. **Object-level** — cross-field validation
3. **Serializer-level** — full data validation

### Examples
```python
class RegisterSerializer(serializers.Serializer):
    email = serializers.EmailField()
    password = serializers.CharField(min_length=8)
    confirm_password = serializers.CharField()
    age = serializers.IntegerField(min_value=18)

    # Field-level validation
    def validate_email(self, value):
        if User.objects.filter(email=value).exists():
            raise serializers.ValidationError("Email already registered")
        return value.lower()

    # Object-level validation
    def validate(self, data):
        if data['password'] != data['confirm_password']:
            raise serializers.ValidationError("Passwords don't match")
        return data
```

### Validators
```python
from rest_framework.validators import UniqueValidator
from django.core.validators import RegexValidator

phone_regex = RegexValidator(regex=r'^\+?1?\d{9,15}$')

class ProfileSerializer(serializers.ModelSerializer):
    username = serializers.CharField(
        validators=[UniqueValidator(queryset=User.objects.all())]
    )
    phone = serializers.CharField(validators=[phone_regex])
```

### Best Practices
- Always validate before `.save()`
- Use `UniqueValidator` for unique fields
- Return clear validation error messages
- Validate file uploads (type, size)

---

## 14. Security Testing

### Simple Definition
Testing your application for security holes before attackers find them.

### Types
- **Unit Testing** — test individual functions
- **Penetration Testing** — simulate attacks
- **SAST** — Static Application Security Testing (code analysis)
- **DAST** — Dynamic Application Security Testing (running app)

### Tools
| Tool | Purpose |
|------|---------|
| `bandit` | Python code security scanner |
| `safety` | Check for vulnerable dependencies |
| `OWASP ZAP` | Web app scanner |
| `django-axes` | Monitor login attempts |

### Bandit Setup
```bash
pip install bandit
bandit -r myproject/  # scan your code
```

### DRF Security Tests
```python
# tests.py
from rest_framework.test import APITestCase

class SecurityTests(APITestCase):
    def test_unauthenticated_access_blocked(self):
        response = self.client.get('/api/private/')
        self.assertEqual(response.status_code, 401)

    def test_sql_injection_blocked(self):
        response = self.client.get("/api/users/?search='; DROP TABLE users;--")
        self.assertNotEqual(response.status_code, 500)

    def test_rate_limit_works(self):
        for _ in range(10):
            self.client.post('/api/login/', {'username': 'x', 'password': 'y'})
        response = self.client.post('/api/login/', {'username': 'x', 'password': 'y'})
        self.assertEqual(response.status_code, 429)  # Too Many Requests
```

### Best Practices
- Run `bandit` and `safety` in CI/CD pipeline
- Test all auth flows
- Check for OWASP Top 10 vulnerabilities
- Use `pytest-django` for test runner

---

## 15. WebSockets

### Simple Definition
WebSockets = a persistent two-way connection. Unlike HTTP where you ask → get answer, WebSocket keeps connection open. Used for real-time features: chat, notifications, live updates.

### HTTP vs WebSocket
```
HTTP:    Client → Request → Server → Response → Connection closed
WS:      Client ←——— persistent connection ———→ Server (both can send anytime)
```

### Setup with Django Channels
```bash
pip install channels channels-redis
```

```python
# settings.py
INSTALLED_APPS += ['channels']
ASGI_APPLICATION = 'myproject.asgi.application'

CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {'hosts': [('127.0.0.1', 6379)]},
    }
}

# asgi.py
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
import myapp.routing

application = ProtocolTypeRouter({
    'http': get_asgi_application(),
    'websocket': AuthMiddlewareStack(
        URLRouter(myapp.routing.websocket_urlpatterns)
    ),
})

# consumers.py
from channels.generic.websocket import AsyncWebsocketConsumer
import json

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = f'chat_{self.room_name}'
        await self.channel_layer.group_add(self.room_group_name, self.channel_name)
        await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

    async def receive(self, text_data):
        data = json.loads(text_data)
        await self.channel_layer.group_send(
            self.room_group_name,
            {'type': 'chat_message', 'message': data['message']}
        )

    async def chat_message(self, event):
        await self.send(text_data=json.dumps({'message': event['message']}))

# routing.py
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/chat/(?P<room_name>\w+)/$', consumers.ChatConsumer.as_asgi()),
]
```

### Best Practices
- Use Redis as channel layer for scalability
- Authenticate WebSocket connections using JWT
- Handle disconnections gracefully
- Use for: chat, notifications, live dashboards

---

## 16. Cron Jobs

### Simple Definition
Tasks that run automatically at scheduled times. Like "send email digest every morning at 8 AM."

### Options
1. **django-crontab** — simple cron
2. **Celery Beat** — distributed scheduled tasks (preferred)
3. **APScheduler** — Python scheduler

### Using Celery Beat (Recommended)
```bash
pip install celery django-celery-beat
```

```python
# settings.py
INSTALLED_APPS += ['django_celery_beat']

CELERY_BEAT_SCHEDULE = {
    'send-daily-email': {
        'task': 'myapp.tasks.send_daily_email',
        'schedule': crontab(hour=8, minute=0),  # 8:00 AM daily
    },
    'cleanup-expired-tokens': {
        'task': 'myapp.tasks.cleanup_tokens',
        'schedule': crontab(minute='*/30'),  # every 30 mins
    },
}

# tasks.py
from celery import shared_task

@shared_task
def send_daily_email():
    users = User.objects.all()
    for user in users:
        send_mail('Daily Update', '...', 'from@example.com', [user.email])
    return f"Sent to {users.count()} users"
```

### Run Celery Beat
```bash
celery -A myproject beat --loglevel=info
```

### Best Practices
- Use Celery Beat instead of system cron for Django apps
- Monitor scheduled tasks with Flower
- Handle failures and retries
- Keep tasks idempotent (safe to run multiple times)

---

## 17. API Documentation with Postman

### Simple Definition
Postman = tool to test and document your APIs. You can send requests, see responses, and share docs with your team or frontend.

### DRF + Postman Workflow
```
1. Build API in Django
2. Test it in Postman
3. Generate Collection → share with frontend
4. OR auto-generate docs with drf-spectacular → import into Postman
```

### Auto-generate OpenAPI for Postman
```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS += ['drf_spectacular']
REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView

urlpatterns += [
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema')),
]
```

### Adding Documentation to Views
```python
from drf_spectacular.utils import extend_schema, OpenApiParameter

class UserListView(generics.ListAPIView):
    @extend_schema(
        summary="List all users",
        description="Returns paginated list of users. Requires authentication.",
        parameters=[
            OpenApiParameter(name='search', description='Search by name', required=False, type=str),
        ],
        responses={200: UserSerializer(many=True)},
    )
    def get(self, request):
        pass
```

### Export from Django → Import into Postman
```bash
# Generate schema file
python manage.py spectacular --color --file schema.yml

# Import schema.yml into Postman via File → Import
```

### Best Practices
- Document all endpoints with descriptions and examples
- Include error responses in documentation
- Use Postman environments (dev, staging, prod)
- Keep docs updated with code changes

---

## 18. Pagination and Its Types

### Simple Definition
Instead of returning all 10,000 records at once, send them in pages. "Page 1: items 1-20, Page 2: items 21-40..."

### Types in DRF

#### 1. PageNumberPagination
```python
# GET /api/users/?page=2
{
    "count": 100,
    "next": "http://api/users/?page=3",
    "previous": "http://api/users/?page=1",
    "results": [...]
}
```

#### 2. LimitOffsetPagination
```python
# GET /api/users/?limit=20&offset=40
{
    "count": 100,
    "next": "...",
    "previous": "...",
    "results": [...]
}
```

#### 3. CursorPagination
```python
# GET /api/users/?cursor=cD0yMDIz
# Cursor is encrypted, more secure, good for real-time data
```

### Setup
```python
# settings.py — global
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
}

# Custom pagination
class StandardPagination(PageNumberPagination):
    page_size = 20
    page_size_query_param = 'page_size'
    max_page_size = 100

class UserListView(generics.ListAPIView):
    pagination_class = StandardPagination
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

### When to Use Which
| Type | When to Use |
|------|-------------|
| PageNumberPagination | General purpose, simple UI |
| LimitOffsetPagination | Flexible queries, analytics |
| CursorPagination | Real-time feeds, social media |

### Best Practices
- Always paginate lists — never return unbounded data
- Default `PAGE_SIZE` = 20-50
- Allow `page_size` override with max limit
- Use cursor pagination for live data feeds

---

## 19. Idempotency

### Simple Definition
An operation is idempotent if doing it multiple times has the same result as doing it once. Like pressing elevator button — pressing it 10 times still calls the elevator once.

### Why It Matters
- Network failures can cause duplicate requests
- Idempotent APIs are safe to retry
- Critical for payments, orders

### In Practice
```python
# Idempotency key — client sends unique key
# Server stores result, returns same response if key seen again

class PaymentView(APIView):
    def post(self, request):
        idempotency_key = request.headers.get('Idempotency-Key')
        
        if idempotency_key:
            cached = cache.get(f'payment:{idempotency_key}')
            if cached:
                return Response(cached)  # return same result
        
        # Process payment
        result = process_payment(request.data)
        
        if idempotency_key:
            cache.set(f'payment:{idempotency_key}', result, timeout=86400)
        
        return Response(result)
```

### Best Practices
- Use UUID as idempotency key
- Store result for 24 hours
- Apply to all POST/PATCH operations that modify state
- Use `get_or_create()` in Django for natural idempotency

---

## 20. Which Methods Are Idempotent

### HTTP Methods Table
| Method | Idempotent | Safe | Description |
|--------|-----------|------|-------------|
| GET | ✅ Yes | ✅ Yes | Read data |
| HEAD | ✅ Yes | ✅ Yes | Read headers only |
| OPTIONS | ✅ Yes | ✅ Yes | API capabilities |
| PUT | ✅ Yes | ❌ No | Full update (replace) |
| DELETE | ✅ Yes | ❌ No | Delete (2nd delete = 404, same state) |
| POST | ❌ No | ❌ No | Create (each call creates new record) |
| PATCH | ❌ No | ❌ No | Partial update (debated) |

### Explanation
```
GET    /users/1          → Returns user 1 (same every time)
PUT    /users/1 {name: "Ali"} → Sets name to Ali (same every time)
DELETE /users/1          → Deletes user (first time: 200, next: 404 but state same)
POST   /users {name: "Ali"}  → Creates NEW user each time ← NOT idempotent
```

### Best Practices
- Use PUT when replacing entire resource
- Use PATCH for partial updates
- Add idempotency keys to POST for payments/critical operations

---

## 21. Redis in DRF

### Simple Definition
Redis = super-fast in-memory database. Used for caching, sessions, throttling, task queues. Much faster than PostgreSQL for frequently accessed data.

### Install & Setup
```bash
pip install redis django-redis
```

```python
# settings.py
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}

# Use Redis for sessions
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
SESSION_CACHE_ALIAS = 'default'
```

### Caching API Responses
```python
from django.core.cache import cache

class ProductListView(APIView):
    def get(self, request):
        cache_key = 'product_list'
        data = cache.get(cache_key)
        
        if not data:
            products = Product.objects.all()
            data = ProductSerializer(products, many=True).data
            cache.set(cache_key, data, timeout=300)  # 5 minutes
        
        return Response(data)
```

### Cache Invalidation
```python
# When product is updated, clear cache
def update(self, request, pk):
    product = Product.objects.get(pk=pk)
    serializer = ProductSerializer(product, data=request.data)
    if serializer.is_valid():
        serializer.save()
        cache.delete('product_list')  # invalidate
        return Response(serializer.data)
```

### Use Cases
| Use Case | How |
|----------|-----|
| API caching | `cache.get/set` |
| Rate limiting | Throttle backend |
| Celery broker | Task queue |
| Sessions | Session backend |
| Pub/Sub | Django Channels |

### Best Practices
- Set sensible TTL (time to live) values
- Invalidate cache on data changes
- Use cache key namespacing: `user:42:profile`
- Monitor Redis memory usage

---

## 22. Celery

### Simple Definition
Celery = runs tasks in the background. Instead of making user wait 30 seconds for email to send, you say "I'll do it later" and respond immediately.

### Install & Setup
```bash
pip install celery redis
```

```python
# myproject/celery.py
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')
app = Celery('myproject')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()

# settings.py
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'

# myproject/__init__.py
from .celery import app as celery_app
__all__ = ('celery_app',)
```

### Creating Tasks
```python
# tasks.py
from celery import shared_task
from django.core.mail import send_mail

@shared_task(bind=True, max_retries=3)
def send_welcome_email(self, user_id):
    try:
        user = User.objects.get(id=user_id)
        send_mail(
            'Welcome!',
            f'Hello {user.name}',
            'noreply@example.com',
            [user.email]
        )
    except Exception as exc:
        raise self.retry(exc=exc, countdown=60)  # retry after 60s
```

### Using in Views
```python
class RegisterView(APIView):
    def post(self, request):
        serializer = RegisterSerializer(data=request.data)
        if serializer.is_valid():
            user = serializer.save()
            send_welcome_email.delay(user.id)  # .delay() = async!
            return Response({'message': 'Registered!'}, status=201)
```

### Run Celery
```bash
celery -A myproject worker --loglevel=info
celery -A myproject flower  # monitoring dashboard
```

### Best Practices
- Always use `.delay()` or `.apply_async()` for async execution
- Use `max_retries` for fault tolerance
- Monitor with Flower
- Set task timeouts to prevent hanging tasks

---

## 23. Kafka

### Simple Definition
Kafka = high-performance message streaming system. Like a super-fast conveyor belt for events. Used when millions of events per second need to be processed.

### Kafka vs Celery
| Feature | Celery | Kafka |
|---------|--------|-------|
| Scale | Medium | Massive |
| Use case | Background tasks | Event streaming |
| Ordering | No guarantee | Per-partition guarantee |
| Replay | No | Yes |
| Learning curve | Easy | Harder |

### Python Kafka Integration
```bash
pip install confluent-kafka
```

```python
# producer.py — send events to Kafka
from confluent_kafka import Producer

producer = Producer({'bootstrap.servers': 'localhost:9092'})

def send_event(topic, data):
    import json
    producer.produce(topic, json.dumps(data).encode('utf-8'))
    producer.flush()

# In views.py
class OrderView(APIView):
    def post(self, request):
        order = create_order(request.data)
        send_event('orders', {'order_id': order.id, 'user': request.user.id})
        return Response({'order_id': order.id})

# consumer.py — process events
from confluent_kafka import Consumer

consumer = Consumer({
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'order-processor',
    'auto.offset.reset': 'earliest'
})
consumer.subscribe(['orders'])

while True:
    msg = consumer.poll(1.0)
    if msg:
        data = json.loads(msg.value())
        process_order(data)
```

### When to Use Kafka
- Millions of events/second
- Multiple services consuming same events
- Need event replay/history
- Microservices architecture

---

## 24. Logs for Debugging

### Simple Definition
Logs = text records of what your app did. Like a flight recorder. When something breaks, you look at logs to understand what happened.

### Log Levels
```
DEBUG    → Detailed info for debugging
INFO     → Normal operations
WARNING  → Something unexpected (but not broken)
ERROR    → Something failed
CRITICAL → System is down
```

### Structured Logging (JSON)
```bash
pip install python-json-logger
```

```python
# settings.py
LOGGING = {
    'version': 1,
    'formatters': {
        'json': {
            '()': 'pythonjsonlogger.jsonlogger.JsonFormatter',
            'format': '%(asctime)s %(name)s %(levelname)s %(message)s'
        }
    },
    'handlers': {
        'json_file': {
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': 'logs/app.log',
            'maxBytes': 10 * 1024 * 1024,  # 10 MB
            'backupCount': 5,
            'formatter': 'json',
        },
    },
    'loggers': {
        'myapp': {'handlers': ['json_file'], 'level': 'DEBUG'},
    }
}
```

### Shipping Logs
- **ELK Stack** — Elasticsearch + Logstash + Kibana
- **CloudWatch** — AWS
- **Datadog** — SaaS monitoring
- **Sentry** — error tracking with context

```bash
pip install sentry-sdk
```

```python
# settings.py
import sentry_sdk
sentry_sdk.init(dsn='your-sentry-dsn', traces_sample_rate=0.1)
```

### Best Practices
- Use structured (JSON) logs for easy parsing
- Never log sensitive data (passwords, tokens, PII)
- Rotate log files to prevent disk full
- Set log level to INFO in production (DEBUG is too verbose)
- Use request ID to trace a request across logs

---

## 25. Making System Scalable

### Simple Definition
Building your system so it can handle 10x, 100x, 1000x more users without rewriting everything.

### Horizontal vs Vertical Scaling
```
Vertical:   1 server with more RAM/CPU (limited)
Horizontal: 10 smaller servers (unlimited, preferred)
```

### Scalability Checklist
```
✅ Stateless API (no server-side session)
✅ Database connection pooling (pgbouncer)
✅ Caching (Redis)
✅ Async tasks (Celery)
✅ CDN for static files
✅ Database read replicas
✅ Containerization (Docker)
✅ Orchestration (Kubernetes)
✅ Message queues (Kafka/RabbitMQ)
✅ Database sharding (extreme scale)
```

### Django Settings for Scale
```python
# Use connection pooling
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'CONN_MAX_AGE': 600,  # reuse connections for 10 min
    }
}

# Multiple databases (primary + replica)
DATABASES = {
    'default': {'...primary...'},
    'replica': {'...replica...'},
}

DATABASE_ROUTERS = ['myapp.routers.PrimaryReplicaRouter']

# Router
class PrimaryReplicaRouter:
    def db_for_read(self, model, **hints):
        return 'replica'

    def db_for_write(self, model, **hints):
        return 'default'
```

### Best Practices
- Start with a monolith, split later
- Cache aggressively (80% of requests should hit cache)
- Use async task queues for heavy work
- Profile before optimizing
- Use `django-silk` or `django-debug-toolbar` for profiling

---

## 26. Mixins and Its Types

### Simple Definition
Mixins = small reusable pieces of code that add specific behavior to views. Like LEGO blocks — combine them to build what you need.

### DRF Mixins
| Mixin | Provides | HTTP Method |
|-------|----------|-------------|
| `CreateModelMixin` | `.create()` | POST |
| `ListModelMixin` | `.list()` | GET (many) |
| `RetrieveModelMixin` | `.retrieve()` | GET (one) |
| `UpdateModelMixin` | `.update()`, `.partial_update()` | PUT, PATCH |
| `DestroyModelMixin` | `.destroy()` | DELETE |

### Using Mixins
```python
from rest_framework import mixins, generics

# Only allow read + create (no update/delete)
class PostView(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    generics.GenericAPIView
):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```

### Generic Views (Mixins Pre-combined)
```python
# These combine mixins for you:
generics.ListAPIView           # GET list
generics.CreateAPIView         # POST
generics.RetrieveAPIView       # GET single
generics.UpdateAPIView         # PUT/PATCH
generics.DestroyAPIView        # DELETE
generics.ListCreateAPIView     # GET list + POST
generics.RetrieveUpdateAPIView # GET + PUT/PATCH
generics.RetrieveDestroyAPIView # GET + DELETE
generics.RetrieveUpdateDestroyAPIView # GET + PUT/PATCH + DELETE
```

### Custom Mixin
```python
class TimestampMixin(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True

class Post(TimestampMixin):
    title = models.CharField(max_length=200)
    # automatically gets created_at and updated_at
```

### Best Practices
- Use generic views for standard CRUD
- Use raw mixins only when you need custom HTTP method names
- Create custom mixins for repeated logic across views

---

## 27. Searching and Fetching Data — Fastest Methods

### Simple Definition
Different ways to find and retrieve data, with different speeds and use cases.

### Methods Overview
| Method | Speed | Use Case |
|--------|-------|---------|
| ORM filter | Fast | Simple lookups |
| `select_related` | Fast | FK joins (1-to-1, FK) |
| `prefetch_related` | Fast | M2M, reverse FK |
| DB Indexes | Very Fast | Large table lookups |
| Full-text search | Fast | Text search in Postgres |
| Elasticsearch | Fastest | Complex text search |
| Redis cache | Fastest | Repeated reads |

### ORM Optimization
```python
# BAD — N+1 problem (100 users = 101 queries!)
posts = Post.objects.all()
for post in posts:
    print(post.author.name)  # query per post!

# GOOD — select_related (JOIN in 1 query)
posts = Post.objects.select_related('author').all()

# GOOD — prefetch_related (2 queries total for M2M)
posts = Post.objects.prefetch_related('tags').all()

# Only fetch needed fields
users = User.objects.only('id', 'name', 'email')

# Defer heavy fields
users = User.objects.defer('bio', 'avatar')  # skip large fields

# Values for raw dicts (faster than model instances)
users = User.objects.values('id', 'name')
```

### DRF Filtering
```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS += ['django_filters']
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend']
}

# views.py
class ProductListView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = [DjangoFilterBackend, SearchFilter, OrderingFilter]
    filterset_fields = ['category', 'in_stock']
    search_fields = ['name', 'description']
    ordering_fields = ['price', 'created_at']
    ordering = ['-created_at']  # default ordering
```

### Best Practices
- Always use `select_related` / `prefetch_related` for related data
- Use `values()` / `values_list()` when you don't need full objects
- Add DB indexes on frequently filtered columns
- Use `explain()` to analyze slow queries

---

## 28. Trigram Similarity and Elasticsearch

### Trigram Similarity (PostgreSQL)

#### Simple Definition
Trigrams break words into groups of 3 characters and compare similarity. "Django" → "Dja", "jan", "ang", "ngo". Useful for fuzzy search — finds "Djanngo" when you search "Django."

```bash
# Add to Django
python manage.py migrate  # after adding pg_trgm extension
```

```python
# In migration
from django.db import migrations

class Migration(migrations.Migration):
    operations = [
        migrations.RunSQL("CREATE EXTENSION IF NOT EXISTS pg_trgm;"),
    ]

# views.py
from django.contrib.postgres.search import TrigramSimilarity

class ProductSearchView(APIView):
    def get(self, request):
        query = request.query_params.get('q', '')
        results = Product.objects.annotate(
            similarity=TrigramSimilarity('name', query)
        ).filter(similarity__gt=0.3).order_by('-similarity')
        return Response(ProductSerializer(results, many=True).data)
```

### Elasticsearch

#### Simple Definition
A separate search engine database. Much more powerful than SQL for full-text search. Used by Wikipedia, GitHub, Netflix.

```bash
pip install django-elasticsearch-dsl
```

```python
# documents.py
from django_elasticsearch_dsl import Document, fields
from django_elasticsearch_dsl.registries import registry
from .models import Product

@registry.register_document
class ProductDocument(Document):
    class Index:
        name = 'products'
        settings = {'number_of_shards': 1}

    class Django:
        model = Product
        fields = ['name', 'description', 'price']

# Search
from .documents import ProductDocument

def search_products(query):
    return ProductDocument.search().query('multi_match', query=query, fields=['name', 'description'])
```

### When to Use What
| Use Case | Tool |
|----------|------|
| Simple text search | `SearchVector` (Postgres) |
| Fuzzy/typo tolerance | Trigram Similarity |
| Complex search + filters | Elasticsearch |
| Autocomplete | Elasticsearch |
| Small dataset | ORM `icontains` |

---

## 29. Permissions

### Simple Definition
Permissions = rules about who can do what. "Is this user allowed to delete this post?"

### Built-in DRF Permissions
```python
from rest_framework.permissions import (
    AllowAny,           # Anyone, no auth needed
    IsAuthenticated,    # Must be logged in
    IsAdminUser,        # Must be admin (is_staff=True)
    IsAuthenticatedOrReadOnly,  # Read=anyone, Write=logged in
)
```

### Custom Permissions
```python
# permissions.py
from rest_framework.permissions import BasePermission

class IsOwnerOrReadOnly(BasePermission):
    """Allow owners to edit, others to read."""
    def has_object_permission(self, request, view, obj):
        if request.method in ('GET', 'HEAD', 'OPTIONS'):
            return True
        return obj.owner == request.user

class HasAPIKey(BasePermission):
    def has_permission(self, request, view):
        api_key = request.headers.get('X-API-Key')
        return APIKey.objects.filter(key=api_key, is_active=True).exists()
```

### Combining Permissions
```python
# AND — both must pass
class MyView(APIView):
    permission_classes = [IsAuthenticated, IsOwnerOrReadOnly]

# OR — any one must pass (DRF 3.9+)
from rest_framework.permissions import OR

class MyView(APIView):
    permission_classes = [IsAdminUser | IsOwnerOrReadOnly]
```

### Object-level vs View-level
```python
class PostDetailView(RetrieveUpdateDestroyAPIView):
    permission_classes = [IsAuthenticated, IsOwnerOrReadOnly]
    queryset = Post.objects.all()
    serializer_class = PostSerializer

    def get_queryset(self):
        # Extra security: filter at queryset level
        return Post.objects.filter(owner=self.request.user)
```

### Best Practices
- Default to `IsAuthenticated` globally
- Override per-view when needed
- Always check object-level permissions for owned resources
- Document permissions in API docs

---

## 30. Atomic Transactions

### Simple Definition
Atomic = all-or-nothing. If you're transferring money from A to B, either BOTH happen or NEITHER happens. No half-completed operations.

### Why It Matters
```
Transfer $100:
  Step 1: Deduct $100 from A   ✅
  Step 2: Add $100 to B        ❌ (server crashes)
  
Without atomic: A lost $100, B got nothing. Money vanished!
With atomic: Both steps rolled back. Nothing happened.
```

### Using atomic()
```python
from django.db import transaction

# Method 1: Decorator
@transaction.atomic
def transfer_money(from_account, to_account, amount):
    from_account.balance -= amount
    from_account.save()
    to_account.balance += amount
    to_account.save()

# Method 2: Context manager
def create_order(user, items):
    with transaction.atomic():
        order = Order.objects.create(user=user)
        for item in items:
            OrderItem.objects.create(order=order, **item)
            # Reduce stock
            item['product'].stock -= item['quantity']
            item['product'].save()
        return order

# Method 3: savepoint (nested transaction)
with transaction.atomic():
    order = Order.objects.create(...)
    with transaction.atomic():
        try:
            send_notification(order)
        except Exception:
            # Only rollback notification, not order
            transaction.savepoint_rollback()
```

### select_for_update (Locking)
```python
with transaction.atomic():
    # Lock the row while we're working with it
    account = Account.objects.select_for_update().get(id=1)
    account.balance -= 100
    account.save()
```

### Best Practices
- Use `atomic` for any multi-step database operations
- Keep transactions short (don't do slow operations inside)
- Use `select_for_update()` to prevent race conditions
- Don't send emails or HTTP requests inside transactions

---

## 31. Types of Views in DRF

### Overview
```
APIView         → Most control, most code
GenericAPIView  → Built-in queryset/serializer handling
Mixins          → Reusable CRUD methods
Generic Views   → Mixins pre-combined (best for simple CRUD)
ViewSet         → Group related views together
ModelViewSet    → Full CRUD in ~5 lines
```

### 1. APIView
```python
from rest_framework.views import APIView
from rest_framework.response import Response

class UserView(APIView):
    def get(self, request):
        users = User.objects.all()
        return Response(UserSerializer(users, many=True).data)

    def post(self, request):
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=201)
        return Response(serializer.errors, status=400)
```

### 2. Generic Views
```python
class UserListCreate(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer

class UserDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

### 3. ViewSet
```python
from rest_framework.viewsets import ViewSet

class UserViewSet(ViewSet):
    def list(self, request):       # GET /users/
        pass
    def create(self, request):     # POST /users/
        pass
    def retrieve(self, request, pk): # GET /users/1/
        pass
    def update(self, request, pk):   # PUT /users/1/
        pass
    def destroy(self, request, pk):  # DELETE /users/1/
        pass
```

### 4. ModelViewSet (Most Common)
```python
from rest_framework.viewsets import ModelViewSet

class UserViewSet(ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAuthenticated]

# urls.py — Router auto-generates all URLs
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register('users', UserViewSet)
urlpatterns = router.urls
```

### When to Use What
| View Type | Use When |
|-----------|---------|
| `APIView` | Custom logic, non-CRUD |
| `GenericAPIView` | Custom with built-in helpers |
| `Generic Views` | Standard CRUD operations |
| `ViewSet` | Custom with action grouping |
| `ModelViewSet` | Full CRUD with minimal code |

---

## 32. Serializers — Complete Guide

### Simple Definition
Serializers = translators. They convert Python objects (models) to JSON (for API response) and JSON to Python (for saving to DB). They also validate data.

### Basic Serializer
```python
from rest_framework import serializers

class UserSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    password = serializers.CharField(write_only=True)  # don't include in response
    created_at = serializers.DateTimeField(read_only=True)
```

### ModelSerializer (Most Used)
```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'created_at']
        read_only_fields = ['id', 'created_at']
        extra_kwargs = {
            'password': {'write_only': True, 'min_length': 8},
        }
```

### Field Types
```python
serializers.CharField()           # text
serializers.IntegerField()        # number
serializers.FloatField()          # decimal
serializers.BooleanField()        # true/false
serializers.EmailField()          # validated email
serializers.URLField()            # validated URL
serializers.DateTimeField()       # datetime
serializers.DateField()           # date only
serializers.ImageField()          # image upload
serializers.FileField()           # file upload
serializers.ChoiceField(choices)  # choice from list
serializers.ListField()           # list of values
serializers.DictField()           # dictionary
serializers.SerializerMethodField() # computed field
```

### SerializerMethodField
```python
class PostSerializer(serializers.ModelSerializer):
    likes_count = serializers.SerializerMethodField()
    is_liked = serializers.SerializerMethodField()

    def get_likes_count(self, obj):
        return obj.likes.count()

    def get_is_liked(self, obj):
        request = self.context.get('request')
        if request and request.user.is_authenticated:
            return obj.likes.filter(user=request.user).exists()
        return False

    class Meta:
        model = Post
        fields = ['id', 'title', 'likes_count', 'is_liked']
```

### Nested Serializers
```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'name']

class PostSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)  # nested!
    author_id = serializers.IntegerField(write_only=True)

    class Meta:
        model = Post
        fields = ['id', 'title', 'author', 'author_id']
```

### Create, Update (PUT), Partial Update (PATCH)
```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'password']

    def create(self, validated_data):
        # Custom create: hash password
        password = validated_data.pop('password')
        user = User(**validated_data)
        user.set_password(password)
        user.save()
        return user

    def update(self, instance, validated_data):
        # Custom update
        password = validated_data.pop('password', None)
        for attr, value in validated_data.items():
            setattr(instance, attr, value)
        if password:
            instance.set_password(password)
        instance.save()
        return instance

# PUT (full update):
serializer = UserSerializer(user, data=request.data)

# PATCH (partial update):
serializer = UserSerializer(user, data=request.data, partial=True)
```

### Read-only and Write-only Fields
```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'password', 'created_at']
        read_only_fields = ['id', 'created_at']      # cannot be set by user
        extra_kwargs = {
            'password': {'write_only': True},  # not in response
        }
```

### Best Practices
- Use `ModelSerializer` for database models
- Use `read_only_fields` for auto-generated fields
- Use `write_only=True` for passwords
- Override `create()` and `update()` for custom logic
- Use `context` to pass request to serializer

---

## 33. Views and Types in DRF

*(See also Topic 31 — Types of Views in DRF)*

### Custom Actions in ViewSet
```python
from rest_framework.decorators import action

class UserViewSet(ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=True, methods=['post'])
    def activate(self, request, pk=None):
        user = self.get_object()
        user.is_active = True
        user.save()
        return Response({'status': 'activated'})

    @action(detail=False, methods=['get'])
    def me(self, request):
        serializer = self.get_serializer(request.user)
        return Response(serializer.data)

# Generated URLs:
# POST /users/{id}/activate/
# GET  /users/me/
```

### Function-Based Views
```python
from rest_framework.decorators import api_view, permission_classes

@api_view(['GET', 'POST'])
@permission_classes([IsAuthenticated])
def user_list(request):
    if request.method == 'GET':
        users = User.objects.all()
        return Response(UserSerializer(users, many=True).data)
    elif request.method == 'POST':
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=201)
        return Response(serializer.errors, status=400)
```

### get_serializer_class for Multiple Serializers
```python
class PostViewSet(ModelViewSet):
    queryset = Post.objects.all()

    def get_serializer_class(self):
        if self.action == 'list':
            return PostListSerializer     # minimal fields
        if self.action == 'create':
            return PostCreateSerializer   # writable fields
        return PostDetailSerializer       # full fields

    def get_queryset(self):
        # User can only see their posts
        return Post.objects.filter(author=self.request.user)
```

---

## 34. Exposing Choice Fields to Frontend

### The Problem
```python
# model.py
class Order(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('processing', 'Processing'),
        ('shipped', 'Shipped'),
        ('delivered', 'Delivered'),
    ]
    status = models.CharField(max_length=20, choices=STATUS_CHOICES)
```

**Frontend asks: "What are the valid status values?"**

### Solution 1: Endpoint for Choices
```python
# views.py
class OrderChoicesView(APIView):
    def get(self, request):
        return Response({
            'status': [
                {'value': choice[0], 'label': choice[1]}
                for choice in Order.STATUS_CHOICES
            ]
        })

# urls.py
path('api/orders/choices/', OrderChoicesView.as_view()),
```

### Solution 2: ChoiceField in Serializer
```python
class OrderSerializer(serializers.ModelSerializer):
    status = serializers.ChoiceField(choices=Order.STATUS_CHOICES)
    status_display = serializers.CharField(source='get_status_display', read_only=True)

    class Meta:
        model = Order
        fields = ['id', 'status', 'status_display']

# Response:
# { "status": "pending", "status_display": "Pending" }
```

### Solution 3: In Swagger/drf-spectacular
```python
# drf-spectacular auto-detects choice fields and shows them in docs!
# Frontend dev sees valid values directly in Swagger UI
```

### Solution 4: metadata() in APIView
```python
# OPTIONS /api/orders/ returns field choices automatically in DRF!
# Frontend can call OPTIONS to get field metadata
```

### Best Practices
- Use Solution 1 for dropdown menus in frontend
- Use `get_status_display` to show human-readable values
- Use drf-spectacular to auto-document choices in Swagger
- Centralize choices in model (single source of truth)

---

## 35. Other Important Backend Topics

### 1. Environment Variables
```python
# Never hardcode secrets!
pip install python-decouple

# .env file
SECRET_KEY=your-secret-key
DB_PASSWORD=dbpassword
DEBUG=False

# settings.py
from decouple import config
SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

### 2. Docker
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000"]
```

### 3. Health Check Endpoint
```python
class HealthCheckView(APIView):
    permission_classes = [AllowAny]
    def get(self, request):
        return Response({'status': 'ok', 'version': '1.0.0'})
```

### 4. API Response Consistency
```python
def success_response(data, message='Success', status=200):
    return Response({'success': True, 'message': message, 'data': data}, status=status)

def error_response(message, errors=None, status=400):
    return Response({'success': False, 'message': message, 'errors': errors}, status=status)
```

### 5. Soft Delete
```python
class SoftDeleteModel(models.Model):
    deleted_at = models.DateTimeField(null=True, blank=True)

    def delete(self, *args, **kwargs):
        self.deleted_at = timezone.now()
        self.save()

    class Meta:
        abstract = True
```

---

## 36. Database Indexing

### Simple Definition
An index = a shortcut to find data fast. Without index, DB reads every row. With index, it jumps directly to the data. Like a book's index vs reading all pages.

### Types of Indexes
| Type | Use Case |
|------|---------|
| B-Tree (default) | Equality and range queries |
| Hash | Equality only |
| GiST | Geometric, full-text |
| GIN | Arrays, JSONB, full-text |
| Partial | Index subset of rows |
| Composite | Multiple columns together |

### Django Index Setup
```python
class Product(models.Model):
    name = models.CharField(max_length=200, db_index=True)  # simple index
    category = models.CharField(max_length=50)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        indexes = [
            models.Index(fields=['category', 'price']),      # composite
            models.Index(fields=['-created_at']),            # descending
            models.Index(
                fields=['name'],
                condition=models.Q(is_active=True),          # partial index
                name='active_products_name_idx'
            ),
        ]
```

### When to Index
```
✅ Foreign keys (automatically indexed)
✅ Fields used in WHERE clause
✅ Fields used in ORDER BY
✅ Fields used in JOIN
❌ Small tables (< 10,000 rows) — not worth it
❌ Columns with low cardinality (boolean, gender)
❌ Columns that are frequently updated
```

### Check Slow Queries
```python
# Use Django's query analysis
queryset = Product.objects.filter(category='electronics')
print(queryset.explain())  # shows query plan

# Or in PostgreSQL
EXPLAIN ANALYZE SELECT * FROM products WHERE category = 'electronics';
```

### Best Practices
- Index foreign keys (Django does this automatically)
- Index columns used in filters and ordering
- Use composite indexes for multi-column queries
- Don't over-index (slows down writes)
- Monitor with `pg_stat_user_indexes`

---

## 37. Query Optimization

### Simple Definition
Writing queries that are fast and don't stress the database. Bad queries can slow down entire application.

### Common Problems and Solutions

#### N+1 Problem
```python
# BAD — 1 + N queries (if 100 posts = 101 queries!)
posts = Post.objects.all()
for post in posts:
    print(post.author.name)  # 1 query per post

# GOOD — 2 queries total
posts = Post.objects.select_related('author').all()

# GOOD for M2M — 2 queries total
posts = Post.objects.prefetch_related('tags').all()
```

#### Count vs len()
```python
# BAD — loads all objects into memory
count = len(Post.objects.all())

# GOOD — COUNT query in DB
count = Post.objects.count()
```

#### exists() vs filter()
```python
# BAD — loads object
if User.objects.filter(email=email).first():
    ...

# GOOD — faster
if User.objects.filter(email=email).exists():
    ...
```

#### Bulk Operations
```python
# BAD — 100 queries
for name in names:
    Product.objects.create(name=name)

# GOOD — 1 query
Product.objects.bulk_create([Product(name=n) for n in names])

# GOOD — bulk update
Product.objects.filter(category='old').update(category='new')
```

#### Django Debug Toolbar
```bash
pip install django-debug-toolbar
# Shows all queries per page in development
```

### Best Practices
- Always use `select_related` / `prefetch_related` for related data
- Use `values()` / `values_list()` when you don't need full objects
- Use `bulk_create` / `bulk_update` for batch operations
- Check query count in development — it should never be N+1
- Add `CONN_MAX_AGE` for connection reuse

---

## 38. Load Balancing

### Simple Definition
Distributing requests across multiple servers so no single server gets overloaded. Like having 10 cashiers instead of 1.

### Types
| Type | How It Works |
|------|-------------|
| Round Robin | Server 1, 2, 3, 1, 2, 3... |
| Least Connections | Send to server with fewest active requests |
| IP Hash | Same client always goes to same server |
| Weighted | Powerful servers get more requests |

### Nginx Load Balancing
```nginx
upstream django_backend {
    least_conn;  # least connections algorithm
    server backend1:8000 weight=3;
    server backend2:8000 weight=1;
    server backend3:8000;
    keepalive 32;
}

server {
    listen 80;
    location / {
        proxy_pass http://django_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Django Must Be Stateless for Load Balancing
```python
# BAD — sessions stored on server (breaks with multiple servers)
request.session['user_id'] = user.id

# GOOD — use JWT (no server state)
# GOOD — store sessions in Redis (shared between servers)
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
SESSION_CACHE_ALIAS = 'default'  # Redis
```

### Best Practices
- Use Redis for sessions and caching (shared state)
- Use JWT for stateless authentication
- Health check endpoint for load balancer: `/health/`
- Use sticky sessions only when absolutely necessary
- Monitor server health with Prometheus + Grafana

---

## 39. Design Patterns

### Simple Definition
Proven solutions to common programming problems. Like recipes for code.

### Common Patterns in Django/DRF

#### 1. Repository Pattern
```python
# Separate DB logic from business logic
class UserRepository:
    def get_by_email(self, email):
        return User.objects.get(email=email)

    def create(self, data):
        return User.objects.create(**data)

class UserService:
    def __init__(self):
        self.repo = UserRepository()

    def register(self, data):
        if self.repo.get_by_email(data['email']):
            raise ValueError("Email exists")
        return self.repo.create(data)
```

#### 2. Service Layer Pattern
```python
# views.py — thin views, no business logic
class RegisterView(APIView):
    def post(self, request):
        result = UserService().register(request.data)
        return Response(result)

# services.py — all business logic here
class UserService:
    def register(self, data):
        # validate, create, send email, log...
        pass
```

#### 3. Factory Pattern
```python
class NotificationFactory:
    @staticmethod
    def create(notification_type):
        if notification_type == 'email':
            return EmailNotification()
        elif notification_type == 'sms':
            return SMSNotification()
        elif notification_type == 'push':
            return PushNotification()
```

#### 4. Observer Pattern (Signals)
```python
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=Order)
def order_created(sender, instance, created, **kwargs):
    if created:
        send_confirmation_email(instance)
        update_inventory(instance)
```

### Best Practices
- Use Service Layer for business logic
- Use Repository for complex DB operations
- Use Django Signals for event-driven actions
- Keep views thin — delegate to services

---

## 40. Clean Architecture

### Simple Definition
Organizing code so that business logic doesn't depend on frameworks, databases, or external services. Easy to test, maintain, and change.

### Layers
```
Presentation Layer:  views.py, serializers.py  ← HTTP details
Business Layer:      services.py              ← Business rules
Data Layer:          repositories.py           ← Database queries
Domain Layer:        models.py                ← Core entities
```

### Example Structure
```
myapp/
├── models.py          # Pure domain models
├── serializers.py     # API contract (presentation)
├── views.py           # HTTP handling (thin!)
├── services/
│   ├── user_service.py    # User business logic
│   └── order_service.py   # Order business logic
├── repositories/
│   ├── user_repo.py       # User DB queries
│   └── order_repo.py      # Order DB queries
└── tests/
    ├── test_services.py   # Test business logic (no HTTP)
    └── test_views.py      # Test API endpoints
```

### Clean View
```python
# views.py — ONLY HTTP concerns
class CreateOrderView(APIView):
    def post(self, request):
        serializer = CreateOrderSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        order = OrderService().create(request.user, serializer.validated_data)
        return Response(OrderSerializer(order).data, status=201)

# services/order_service.py — ONLY business logic
class OrderService:
    def create(self, user, data):
        if not user.can_order():
            raise ValidationError("Account suspended")
        with transaction.atomic():
            order = Order.objects.create(user=user, **data)
            self._update_inventory(order)
            self._send_confirmation(order)
        return order
```

### Best Practices
- Thin views, fat services
- Business logic should be testable without Django test client
- Don't import models in services — use repositories
- Follow Single Responsibility Principle

---

## 41. OAuth2

### Simple Definition
OAuth2 = let users log in with Google/GitHub/Facebook without sharing their password with your app. "Login with Google" is OAuth2.

### Flow
```
1. User clicks "Login with Google"
2. Redirect to Google login
3. User grants permission
4. Google redirects back with "code"
5. Your server exchanges code for access token
6. Use token to get user info from Google
7. Create/login user in your system
```

### Setup with django-allauth
```bash
pip install django-allauth
```

```python
# settings.py
INSTALLED_APPS += [
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google',
    'allauth.socialaccount.providers.github',
]

SITE_ID = 1
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'APP': {
            'client_id': 'your-google-client-id',
            'secret': 'your-google-secret',
        },
        'SCOPE': ['email', 'profile'],
    }
}

# urls.py
urlpatterns += [
    path('accounts/', include('allauth.urls')),
]
```

### DRF + JWT + Social Auth
```bash
pip install dj-rest-auth
```

```python
# After social login, get JWT token
POST /auth/google/
{
    "access_token": "google-token-here"
}
# Returns your app's JWT access/refresh tokens
```

### Best Practices
- Store client secrets in environment variables
- Use PKCE for mobile apps
- Validate email from provider
- Handle account linking (user logs in with Google and email)

---

## 42. OpenAPI / Swagger

### Simple Definition
OpenAPI = standard format to describe your API. Swagger = tool to display it as interactive documentation. Frontend developers love it.

### Setup with drf-spectacular
```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS += ['drf_spectacular']
REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'My API',
    'DESCRIPTION': 'Backend API documentation',
    'VERSION': '1.0.0',
}

# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView, SpectacularRedocView

urlpatterns += [
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema')),   # Swagger UI
    path('api/redoc/', SpectacularRedocView.as_view(url_name='schema')),   # ReDoc UI
]
```

### Adding Docs to Views
```python
from drf_spectacular.utils import extend_schema

class UserViewSet(ModelViewSet):
    @extend_schema(
        summary="Create a new user",
        description="Register a new user account. Returns JWT tokens.",
        responses={
            201: UserSerializer,
            400: OpenApiResponse(description="Validation errors"),
        },
        tags=['Authentication'],
    )
    def create(self, request):
        pass
```

### Best Practices
- Tag endpoints by feature (`Authentication`, `Users`, `Orders`)
- Document error responses, not just success
- Keep schema updated — use CI check
- Export schema and import into Postman

---

## 43. Testing — Unit and Integration

### Simple Definition
- **Unit Test** — test one small piece in isolation (one function)
- **Integration Test** — test multiple pieces working together (API endpoint + DB)

### Setup
```bash
pip install pytest pytest-django factory-boy
```

```python
# pytest.ini
[pytest]
DJANGO_SETTINGS_MODULE = myproject.settings
python_files = tests.py test_*.py *_test.py
```

### Factory (Test Data)
```python
# factories.py
import factory
from django.contrib.auth.models import User
from .models import Post

class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User

    username = factory.Sequence(lambda n: f'user{n}')
    email = factory.LazyAttribute(lambda u: f'{u.username}@test.com')
    password = factory.PostGenerationMethodCall('set_password', 'password123')

class PostFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Post

    title = factory.Faker('sentence')
    author = factory.SubFactory(UserFactory)
```

### Unit Tests
```python
# test_services.py
from .services import calculate_discount

def test_ten_percent_discount():
    assert calculate_discount(100, 10) == 90

def test_zero_discount():
    assert calculate_discount(100, 0) == 100
```

### Integration/API Tests
```python
# test_views.py
import pytest
from rest_framework.test import APIClient
from .factories import UserFactory, PostFactory

@pytest.mark.django_db
class TestPostAPI:
    def setup_method(self):
        self.client = APIClient()
        self.user = UserFactory()
        self.client.force_authenticate(user=self.user)

    def test_list_posts(self):
        PostFactory.create_batch(3)
        response = self.client.get('/api/posts/')
        assert response.status_code == 200
        assert len(response.data['results']) == 3

    def test_create_post(self):
        data = {'title': 'Test Post', 'content': 'Some content'}
        response = self.client.post('/api/posts/', data)
        assert response.status_code == 201
        assert response.data['title'] == 'Test Post'

    def test_unauthenticated_blocked(self):
        self.client.logout()
        response = self.client.get('/api/posts/')
        assert response.status_code == 401
```

### Best Practices
- Aim for 80%+ code coverage
- Use `factory-boy` for test data
- Test authentication, authorization, validation
- Run tests in CI/CD on every push
- Keep tests independent (no shared state)

---

## 44. SOLID Principles

### Simple Definition
5 rules for writing clean, maintainable code. The acronym SOLID.

### S — Single Responsibility Principle
```python
# BAD — UserView does everything
class UserView(APIView):
    def post(self, request):
        # validate data
        # create user
        # send email
        # log action
        # all in one method!

# GOOD — each class has one job
class UserSerializer(serializers.ModelSerializer): ...  # validation
class UserService: ...                                   # business logic
class EmailService: ...                                  # email sending
class AuditService: ...                                  # logging
```

### O — Open/Closed Principle
```python
# Open for extension, closed for modification
class BaseNotification:
    def send(self, user, message): raise NotImplementedError

class EmailNotification(BaseNotification):
    def send(self, user, message): send_email(user.email, message)

class SMSNotification(BaseNotification):
    def send(self, user, message): send_sms(user.phone, message)

# Add new notification type WITHOUT modifying existing code
class PushNotification(BaseNotification):
    def send(self, user, message): send_push(user.device_token, message)
```

### L — Liskov Substitution Principle
```python
# Child class should work wherever parent class is expected
class Shape:
    def area(self): raise NotImplementedError

class Circle(Shape):
    def area(self): return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def area(self): return self.width * self.height

# Both work the same way when used as Shape
def print_area(shape: Shape):
    print(shape.area())  # works for Circle and Rectangle
```

### I — Interface Segregation Principle
```python
# Don't force classes to implement methods they don't need
# BAD — one big interface
class IRepository:
    def read(self): ...
    def write(self): ...
    def delete(self): ...

# GOOD — split interfaces
class IReadRepository:
    def read(self): ...

class IWriteRepository:
    def write(self): ...

class ReadOnlyCache(IReadRepository):
    def read(self): ...
    # doesn't need write/delete!
```

### D — Dependency Inversion Principle
```python
# High-level modules should not depend on low-level modules
# BAD — view depends directly on specific service
class UserView(APIView):
    def post(self, request):
        service = MySQLUserService()  # tightly coupled!
        service.create(request.data)

# GOOD — inject dependency
class UserView(APIView):
    def __init__(self, user_service=None):
        self.user_service = user_service or UserService()

    def post(self, request):
        self.user_service.create(request.data)
# Now easy to swap service in tests: UserView(user_service=MockUserService())
```

### Summary
| Letter | Principle | Key Idea |
|--------|-----------|---------|
| S | Single Responsibility | One class, one job |
| O | Open/Closed | Extend without modifying |
| L | Liskov Substitution | Child works as parent |
| I | Interface Segregation | Small interfaces |
| D | Dependency Inversion | Depend on abstractions |

---

*End of Notes — Django REST Framework Complete Guide*
*Topics Covered: 44/44 ✅*
