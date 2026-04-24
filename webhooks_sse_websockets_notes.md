# Complete Notes: Webhooks, Server-Sent Events & WebSockets in Django

## Table of Contents

1. [Foundations](#1-foundations)
   - [1.1 HTTP Basics](#11-http-basics)
   - [1.2 Networking Concepts](#12-networking-concepts)
   - [1.3 Client–Server Architecture](#13-clientserver-architecture)
   - [1.4 JSON & Serialization](#14-json--serialization)
2. [Real-Time Communication Overview](#2-real-time-communication-overview)
   - [2.1 What is Real-Time Communication](#21-what-is-real-time-communication)
   - [2.2 Communication Models](#22-communication-models)
   - [2.3 Comparison Overview](#23-comparison-overview)
3. [Webhooks](#3-webhooks)
   - [3.1 Basics of Webhooks](#31-basics-of-webhooks)
   - [3.2 Webhook Flow](#32-webhook-flow)
   - [3.3 Webhook Implementation in Django](#33-webhook-implementation-in-django)
   - [3.4 Security in Webhooks](#34-security-in-webhooks)
   - [3.5 Reliability](#35-reliability)
   - [3.6 Webhook Testing](#36-webhook-testing)
   - [3.7 Advanced Webhooks](#37-advanced-webhooks)
4. [Server-Sent Events (SSE)](#4-server-sent-events-sse)
   - [4.1 Basics of SSE](#41-basics-of-sse)
   - [4.2 How SSE Works](#42-how-sse-works)
   - [4.3 SSE vs Polling](#43-sse-vs-polling)
   - [4.4 Implementing SSE in Django](#44-implementing-sse-in-django)
   - [4.5 Frontend Integration](#45-frontend-integration)
   - [4.6 SSE Features](#46-sse-features)
   - [4.7 Limitations of SSE](#47-limitations-of-sse)
   - [4.8 Advanced SSE](#48-advanced-sse)
5. [WebSockets](#5-websockets)
   - [5.1 Basics of WebSockets](#51-basics-of-websockets)
   - [5.2 WebSocket Handshake](#52-websocket-handshake)
   - [5.3 WebSocket Lifecycle](#53-websocket-lifecycle)
   - [5.4 WebSocket Frames](#54-websocket-frames)
   - [5.5 Implementing WebSockets in Django](#55-implementing-websockets-in-django)
   - [5.6 Routing in WebSockets](#56-routing-in-websockets)
   - [5.7 Broadcasting Messages](#57-broadcasting-messages)
   - [5.8 Scaling WebSockets](#58-scaling-websockets)
   - [5.9 Security in WebSockets](#59-security-in-websockets)
   - [5.10 Error Handling](#510-error-handling)
6. [Comparison & Decision Making](#6-comparison--decision-making)
7. [Backend Architecture Patterns](#7-backend-architecture-patterns)
8. [Performance & Scaling](#8-performance--scaling)
9. [Security Best Practices](#9-security-best-practices)
10. [Advanced Topics](#10-advanced-topics)
11. [Practical Projects](#11-practical-projects)
12. [Tools & Libraries](#12-tools--libraries)

---

# 1. Foundations

## 1.1 HTTP Basics

### Simple Definition
HTTP (HyperText Transfer Protocol) is the language that browsers and servers use to talk to each other. When you open a website, your browser sends a **request** and the server sends back a **response**.

### Why It Is Used
Every API, website, and web app uses HTTP. It is the foundation of all web communication.

### Request / Response Cycle
```
Client                    Server
  |                          |
  |-- HTTP Request --------> |
  |                          | (processes request)
  |<-- HTTP Response --------|
```

### HTTP Methods
| Method | Purpose | Example |
|--------|---------|---------|
| GET | Read data | Get list of users |
| POST | Create data | Register new user |
| PUT | Replace all data | Update full profile |
| PATCH | Update part of data | Update only username |
| DELETE | Remove data | Delete a post |

### Headers, Body, Status Codes
- **Headers** — Extra info sent with request/response (e.g., `Content-Type: application/json`)
- **Body** — The actual data being sent (JSON, text, etc.)
- **Status Codes** — Server's reply code:
  - `200` = OK
  - `201` = Created
  - `400` = Bad Request
  - `401` = Unauthorized
  - `404` = Not Found
  - `500` = Server Error

### Stateless Nature of HTTP
- HTTP does **not remember** previous requests
- Every request is independent
- State must be managed via cookies, tokens, or sessions

### Connection Lifecycle
1. Client opens TCP connection
2. Sends HTTP request
3. Server responds
4. Connection closes (or stays open for reuse with `keep-alive`)

---

## 1.2 Networking Concepts

### TCP vs UDP
| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | Guaranteed delivery | No guarantee |
| Speed | Slower | Faster |
| Use Case | HTTP, APIs, WebSockets | Video streaming, games |
| Order | Ordered packets | Unordered |

### Persistent vs Non-Persistent Connections
- **Non-persistent** — New TCP connection for each request (HTTP/1.0)
- **Persistent** — Reuse same connection for multiple requests (HTTP/1.1+)

### Ports & Sockets
- **Port** — A number that identifies a service (e.g., port 80 for HTTP, 443 for HTTPS, 8000 for Django dev)
- **Socket** — A combination of IP address + port (e.g., `192.168.1.1:8000`)

### DNS and Domain Resolution
1. You type `google.com`
2. DNS converts it to IP address `142.250.x.x`
3. Browser connects to that IP

---

## 1.3 Client–Server Architecture

### REST APIs
- **REST** (Representational State Transfer) is a way to design APIs using HTTP
- Uses standard methods: GET, POST, PUT, PATCH, DELETE
- Stateless — no session stored on server

### Polling vs Push-based Systems
| Type | How it Works | Problem |
|------|-------------|---------|
| Polling | Client asks server every X seconds | Wastes resources |
| Long Polling | Client waits until server has data | Still HTTP overhead |
| Push (SSE/WS) | Server pushes data when ready | Best for real-time |

### Latency and Bandwidth Basics
- **Latency** — Time delay between request and response (lower is better)
- **Bandwidth** — How much data can be transferred (higher is better)

---

## 1.4 JSON & Serialization

### JSON Structure
```json
{
  "name": "Alice",
  "age": 25,
  "skills": ["Python", "Django"],
  "address": {
    "city": "Mumbai",
    "country": "India"
  }
}
```

### Serialization / Deserialization
- **Serialization** — Converting Python object → JSON string (for sending)
- **Deserialization** — Converting JSON string → Python object (for reading)

```python
import json

# Serialization
data = {"name": "Alice", "age": 25}
json_string = json.dumps(data)   # Python dict → JSON string

# Deserialization
parsed = json.loads(json_string)  # JSON string → Python dict
print(parsed["name"])  # Alice
```

### Content-Type Header
- Always set `Content-Type: application/json` when sending JSON data
- Django REST Framework does this automatically

---

# 2. Real-Time Communication Overview

## 2.1 What is Real-Time Communication

### Simple Definition
Real-time communication means data is sent and received **immediately** — without the user having to refresh or request it manually. Like WhatsApp messages — you see them the moment someone sends them.

### Use Cases
- **Chat apps** — WhatsApp, Slack messages
- **Notifications** — Email alerts, order updates
- **Live dashboards** — Stock prices, analytics
- **Stock updates** — Live price feeds

---

## 2.2 Communication Models

### Polling
- Client asks server: *"Any new data?"* every few seconds
- Simple but **wasteful** — most requests return nothing

```
Client: "Any news?" → Server: "No"
Client: "Any news?" → Server: "No"
Client: "Any news?" → Server: "Yes, here it is!"
```

### Long Polling
- Client sends request and **waits**
- Server holds connection open until it has data, then responds
- Client immediately sends another request

```
Client: "Any news?" → Server: (waits 30 seconds...) → "Yes, here!"
Client: "Any news?" → Server: (waits...) → "Yes!"
```

### Push-Based Systems
- Server **pushes** data to client the moment it is available
- No need to ask — server tells you
- Used by SSE and WebSockets

---

## 2.3 Comparison Overview

| Feature | Webhooks | SSE | WebSockets |
|---------|----------|-----|------------|
| Direction | Server → Server | Server → Client | Both ways |
| Connection | Short-lived HTTP | Persistent HTTP | Persistent WS |
| Protocol | HTTP | HTTP | WebSocket (ws://) |
| Use Case | Event notifications | Live updates, feeds | Chat, games |
| Browser Support | N/A (server-to-server) | Good | Excellent |
| Complexity | Low | Low | Medium |

---

# 3. Webhooks

## 3.1 Basics of Webhooks

### Simple Definition
A webhook is like a **doorbell**. Instead of you going to the door every minute to check if someone is there (polling), the doorbell **rings you** when someone arrives. In web terms: a service calls **your URL** when something happens.

### Why It Is Used
- Payment gateways (Stripe, Razorpay) notify you when a payment succeeds
- GitHub notifies your server when someone pushes code
- You don't need to keep checking — they **tell you**

### How It Works (Event → HTTP POST)
```
Payment Service (Stripe)          Your Server
        |                               |
        | -- HTTP POST /webhook/ -----> |
        |    { "event": "payment_success", "amount": 500 }
        |                               |
        |<-- 200 OK -------------------|
```

### Producer vs Consumer
- **Producer** — The service that sends the webhook (e.g., Stripe)
- **Consumer** — Your server that receives and processes it (e.g., your Django app)

### Types of Webhooks
1. **Synchronous** — Waits for your server to respond before continuing
2. **Asynchronous** — Fires and doesn't wait for response
3. **Retry-based** — Tries again if your server fails to respond

### Advantages
- No polling needed
- Real-time notifications
- Simple to implement
- Works with any language

### Disadvantages
- Your server must be publicly accessible (no localhost)
- You must handle failures and retries
- Can receive duplicate events (requires idempotency)
- No built-in browser support

### Tips and Tricks
- Always return `200 OK` quickly, do heavy processing in background
- Use **idempotency keys** to avoid processing same event twice
- Log every incoming webhook for debugging

### Best Practices
- Verify webhook signatures before processing
- Respond within 5 seconds (use async tasks for heavy work)
- Store raw payload before processing
- Set up retry handling

---

## 3.2 Webhook Flow

```
1. An event happens on the external service (e.g., payment success)
2. Service generates a payload (JSON data about the event)
3. Service sends HTTP POST to your registered webhook URL
4. Your server receives and verifies the request
5. Your server processes the data
6. Your server returns 200 OK
```

---

## 3.3 Webhook Implementation in Django

### Step-by-Step Setup

**1. Install Django REST Framework**
```bash
pip install django djangorestframework
```

**2. Settings (`settings.py`)**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'webhooks',  # your app
]
```

**3. Create the App**
```bash
python manage.py startapp webhooks
```

**4. Model (`models.py`) — Store incoming webhooks**
```python
from django.db import models

class WebhookEvent(models.Model):
    event_type = models.CharField(max_length=100)
    payload = models.JSONField()
    received_at = models.DateTimeField(auto_now_add=True)
    processed = models.BooleanField(default=False)

    def __str__(self):
        return f"{self.event_type} - {self.received_at}"
```

**5. View (`views.py`) — Receive the webhook**
```python
import json
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from django.views.decorators.http import require_POST
from .models import WebhookEvent

@csrf_exempt  # Webhooks come from external servers, so disable CSRF
@require_POST
def webhook_receiver(request):
    try:
        # Parse the incoming JSON payload
        payload = json.loads(request.body)

        # Save to database
        event = WebhookEvent.objects.create(
            event_type=payload.get("event", "unknown"),
            payload=payload
        )

        # Process based on event type
        if payload.get("event") == "payment_success":
            handle_payment(payload)

        return JsonResponse({"status": "received"}, status=200)

    except json.JSONDecodeError:
        return JsonResponse({"error": "Invalid JSON"}, status=400)


def handle_payment(payload):
    # Your business logic here
    amount = payload.get("amount")
    print(f"Payment received: {amount}")
```

**6. URLs (`urls.py`)**
```python
# webhooks/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('webhook/', views.webhook_receiver, name='webhook'),
]

# project/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('webhooks.urls')),
]
```

**7. Run Migrations**
```bash
python manage.py makemigrations
python manage.py migrate
```

**Real API Example — Test with curl**
```bash
curl -X POST http://localhost:8000/api/webhook/ \
  -H "Content-Type: application/json" \
  -d '{"event": "payment_success", "amount": 500, "user": "alice"}'
```

---

## 3.4 Security in Webhooks

### Simple Definition
Webhooks receive data from external services. Anyone could send fake data to your endpoint if it is unprotected. You need to **verify** that requests really come from the trusted service.

### Secret Tokens
- The external service and your server share a **secret key**
- Every request includes a signature built from that key
- You verify the signature to confirm authenticity

### Signature Verification (HMAC)

```python
import hmac
import hashlib
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt

WEBHOOK_SECRET = "your_secret_key_here"

def verify_signature(payload_body, signature_header):
    """Verify that the webhook came from the trusted source."""
    expected = hmac.new(
        WEBHOOK_SECRET.encode("utf-8"),
        payload_body,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature_header)

@csrf_exempt
def secure_webhook(request):
    signature = request.headers.get("X-Signature-256", "")

    if not verify_signature(request.body, signature):
        return JsonResponse({"error": "Invalid signature"}, status=403)

    payload = json.loads(request.body)
    # Process safely...
    return JsonResponse({"status": "ok"})
```

### IP Whitelisting
- Only allow requests from known IP addresses of the service

```python
ALLOWED_IPS = ["140.82.112.0", "192.30.252.0"]  # GitHub IPs for example

def check_ip(request):
    client_ip = request.META.get("REMOTE_ADDR")
    if client_ip not in ALLOWED_IPS:
        return False
    return True
```

### Replay Attack Prevention
- A **replay attack** = attacker captures a valid request and sends it again
- Add a **timestamp** to the signature and reject old requests (> 5 minutes old)

```python
import time

def is_request_fresh(timestamp_str, max_age_seconds=300):
    """Return True if request is less than 5 minutes old."""
    try:
        timestamp = int(timestamp_str)
        return abs(time.time() - timestamp) < max_age_seconds
    except (ValueError, TypeError):
        return False
```

---

## 3.5 Reliability

### Retry Mechanisms
- External services retry if they get no response or get a 5xx error
- Your server must be **idempotent** — same event processed twice = same result

### Idempotency
```python
def webhook_receiver(request):
    payload = json.loads(request.body)
    event_id = payload.get("id")

    # Check if already processed
    if WebhookEvent.objects.filter(event_id=event_id, processed=True).exists():
        return JsonResponse({"status": "already processed"}, status=200)

    # Process and mark as done
    event = WebhookEvent.objects.create(event_id=event_id, payload=payload)
    process_event(event)
    event.processed = True
    event.save()

    return JsonResponse({"status": "ok"})
```

### Logging and Monitoring
```python
import logging
logger = logging.getLogger(__name__)

@csrf_exempt
def webhook_receiver(request):
    logger.info(f"Webhook received: {request.body[:200]}")
    # ... process
    logger.info("Webhook processed successfully")
```

---

## 3.6 Webhook Testing

### Tools
- **ngrok** — Exposes your `localhost` to the internet so external services can reach it
- **webhook.site** — A free site that gives you a test URL and shows all incoming requests

### ngrok Setup
```bash
# Install ngrok, then run:
ngrok http 8000

# You get a public URL like:
# https://abc123.ngrok.io
# Register this as your webhook URL in Stripe/GitHub/etc.
```

### Local Testing Strategy
```bash
# Terminal 1: Run Django
python manage.py runserver

# Terminal 2: Run ngrok
ngrok http 8000

# Test with curl
curl -X POST https://abc123.ngrok.io/api/webhook/ \
  -H "Content-Type: application/json" \
  -d '{"event": "test", "data": "hello"}'
```

---

## 3.7 Advanced Webhooks

### Event Versioning
- Add version to your webhook payload to handle changes over time

```python
# Producer sends:
{
  "version": "2.0",
  "event": "payment_success",
  "data": { ... }
}

# Consumer handles by version:
def process_webhook(payload):
    version = payload.get("version", "1.0")
    if version == "2.0":
        handle_v2(payload)
    else:
        handle_v1(payload)
```

### Queue-Based Webhook Delivery (Celery)
- Instead of processing in the view (slow), push to a **task queue** and respond fast

```bash
pip install celery redis
```

```python
# tasks.py
from celery import shared_task

@shared_task
def process_webhook_task(payload):
    """This runs in the background."""
    event_type = payload.get("event")
    if event_type == "payment_success":
        # slow processing here
        pass

# views.py
from .tasks import process_webhook_task

@csrf_exempt
def webhook_receiver(request):
    payload = json.loads(request.body)
    process_webhook_task.delay(payload)  # async, non-blocking
    return JsonResponse({"status": "queued"}, status=200)
```

### Rate Limiting
```python
# Use django-ratelimit
pip install django-ratelimit

from ratelimit.decorators import ratelimit

@ratelimit(key='ip', rate='100/h', method='POST', block=True)
@csrf_exempt
def webhook_receiver(request):
    ...
```

---

# 4. Server-Sent Events (SSE)

## 4.1 Basics of SSE

### Simple Definition
SSE is like subscribing to a **newspaper delivery**. Once you subscribe (connect), the server **keeps sending you updates** automatically whenever there is new content. It is one-way: server → client only.

### Why It Is Used
- Live notifications
- Real-time dashboards (analytics, stock prices)
- News feeds
- Progress updates for long tasks

### Advantages
- Simple to implement
- Works over regular HTTP (no new protocol)
- Auto-reconnects on disconnect
- Browser support is very good
- Works through firewalls (uses HTTP)

### Disadvantages
- One-way only (client cannot send messages back)
- Limited to text data (no binary)
- Browsers limit to 6 connections per domain

### Tips and Tricks
- Use SSE when you only need server → client updates
- Add event IDs for reliable reconnection
- Use Redis Pub/Sub for broadcasting to multiple clients

### Best Practices
- Always set `Cache-Control: no-cache`
- Include `retry:` field to control reconnect timing
- Close the connection when the client disconnects

---

## 4.2 How SSE Works

### Persistent HTTP Connection
```
Client                        Server
  |                              |
  |-- GET /events/ (EventSource) |
  |                              |
  |<-- text/event-stream --------|
  |<-- data: hello\n\n ----------|
  |<-- data: world\n\n ----------|
  |<-- data: {"price": 100}\n\n--|
  |         (connection stays open forever)
```

### Event Format
```
data: Simple message\n\n

event: custom_event\n
data: {"key": "value"}\n\n

id: 42\n
data: Message with ID\n\n

retry: 3000\n
data: Reconnect every 3 seconds\n\n
```

- `data:` — The actual content
- `event:` — Custom event name (default is `message`)
- `id:` — Event ID for tracking
- `retry:` — Reconnect interval in milliseconds
- **Two newlines `\n\n`** signal the end of one event

---

## 4.3 SSE vs Polling

| Feature | Polling | SSE |
|---------|---------|-----|
| Connections | New request every N seconds | One persistent connection |
| Server Load | High (many requests) | Low (one connection) |
| Latency | Delayed by interval | Instant |
| Bandwidth | Wasted on empty responses | Only real data |
| Implementation | Very simple | Simple |

---

## 4.4 Implementing SSE in Django

### Step-by-Step Setup

**1. Install Django**
```bash
pip install django djangorestframework
```

**2. Settings (`settings.py`)**
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'sse_app',
]
```

**3. Create App**
```bash
python manage.py startapp sse_app
```

**4. View (`views.py`) — Stream events**
```python
import time
import json
from django.http import StreamingHttpResponse
from django.views.decorators.http import require_GET

def event_stream():
    """Generator that yields SSE-formatted events."""
    event_id = 0
    while True:
        event_id += 1
        data = {
            "message": f"Update #{event_id}",
            "timestamp": time.time()
        }
        # SSE format: must end with double newline
        yield f"id: {event_id}\n"
        yield f"data: {json.dumps(data)}\n\n"
        time.sleep(2)  # Send update every 2 seconds

@require_GET
def sse_view(request):
    response = StreamingHttpResponse(
        event_stream(),
        content_type="text/event-stream"
    )
    response["Cache-Control"] = "no-cache"
    response["X-Accel-Buffering"] = "no"  # Disable Nginx buffering
    return response
```

**5. Custom Event Types**
```python
def event_stream_custom():
    while True:
        # Named event
        yield "event: notification\n"
        yield f'data: {{"msg": "New order placed!"}}\n\n'
        time.sleep(5)

        # Default message event
        yield f'data: {{"status": "alive"}}\n\n'
        time.sleep(1)
```

**6. URLs (`urls.py`)**
```python
# sse_app/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('events/', views.sse_view, name='sse'),
]

# project/urls.py
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('sse_app.urls')),
]
```

**Real API Example — Test with curl**
```bash
curl -N http://localhost:8000/api/events/
# -N disables buffering so you see output immediately
```

---

## 4.5 Frontend Integration

### EventSource API (JavaScript)
```html
<!DOCTYPE html>
<html>
<head><title>SSE Demo</title></head>
<body>
  <ul id="messages"></ul>

  <script>
    // Connect to SSE endpoint
    const source = new EventSource("/api/events/");

    // Handle default 'message' events
    source.onmessage = function(event) {
      const data = JSON.parse(event.data);
      const li = document.createElement("li");
      li.textContent = data.message;
      document.getElementById("messages").appendChild(li);
    };

    // Handle custom named events
    source.addEventListener("notification", function(event) {
      const data = JSON.parse(event.data);
      alert(data.msg);
    });

    // Handle connection errors
    source.onerror = function() {
      console.log("Connection lost, reconnecting...");
    };

    // Close connection when done
    // source.close();
  </script>
</body>
</html>
```

---

## 4.6 SSE Features

### Auto-Reconnect
- Browser automatically reconnects if connection drops
- Sends `Last-Event-ID` header with the last received ID
- Server can resume from that point

```python
def event_stream(request):
    last_id = int(request.META.get("HTTP_LAST_EVENT_ID", 0))
    event_id = last_id  # Continue from where client left off

    while True:
        event_id += 1
        yield f"id: {event_id}\n"
        yield f"data: Event #{event_id}\n\n"
        time.sleep(1)
```

### Retry Intervals
```python
def event_stream():
    # Tell client to retry connection after 5 seconds
    yield "retry: 5000\n\n"
    while True:
        yield f"data: hello\n\n"
        time.sleep(1)
```

---

## 4.7 Limitations of SSE

- **One-way only** — Client can't send data back (use REST API for that)
- **Browser connection limits** — Max 6 SSE connections per domain per browser
- **No binary support** — Text only (use WebSocket for binary)
- **Proxies and firewalls** — Some may buffer the stream and break SSE

---

## 4.8 Advanced SSE

### Scaling with Redis Pub/Sub

**Setup**
```bash
pip install redis django-redis
```

**Redis Pub/Sub for Broadcasting**
```python
import redis
import json
from django.http import StreamingHttpResponse

redis_client = redis.Redis(host='localhost', port=6379, db=0)

def event_stream_redis(channel="notifications"):
    pubsub = redis_client.pubsub()
    pubsub.subscribe(channel)

    for message in pubsub.listen():
        if message["type"] == "message":
            data = message["data"].decode("utf-8")
            yield f"data: {data}\n\n"

def sse_redis_view(request):
    return StreamingHttpResponse(
        event_stream_redis(),
        content_type="text/event-stream"
    )

# Publishing from anywhere in Django:
def publish_notification(message):
    redis_client.publish("notifications", json.dumps(message))
```

---

# 5. WebSockets

## 5.1 Basics of WebSockets

### Simple Definition
WebSocket is like a **phone call**. Once connected, both sides can talk at any time. Unlike HTTP where you make one request and get one response, WebSocket keeps the line open so both sides can send messages **simultaneously**.

### Why It Is Used
- Real-time chat applications
- Multiplayer online games
- Collaborative editing (Google Docs-like)
- Live auctions or bidding
- Real-time location tracking

### Advantages
- Bidirectional — both server and client can send anytime
- Low latency — no HTTP overhead after initial connection
- Full-duplex — send and receive simultaneously
- Supports binary data

### Disadvantages
- More complex to implement
- Requires ASGI server (not regular WSGI)
- Scaling needs extra setup (Redis channel layer)
- Some proxies may not support WebSockets

### Tips and Tricks
- Use Django Channels for WebSocket support in Django
- Always handle disconnect events
- Use group channels for chat rooms / broadcasting
- Use ping/pong frames to detect dead connections

### Best Practices
- Authenticate on connect (not on every message)
- Validate all incoming messages
- Handle disconnect gracefully
- Use Redis channel layer for scaling beyond single server

---

## 5.2 WebSocket Handshake

### HTTP Upgrade Request
WebSocket starts as HTTP then **upgrades**:

```
Client:
GET /ws/chat/ HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==

Server:
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

- After `101 Switching Protocols`, the connection is a WebSocket
- URL changes from `http://` to `ws://` (or `https://` to `wss://`)

---

## 5.3 WebSocket Lifecycle

```
Client                              Server
  |                                    |
  |-- HTTP Upgrade Request ----------> |
  |<-- 101 Switching Protocols --------|
  |         [Connected]                |
  |-- "Hello!" ----------------------> |  (message)
  |<-- "Hi there!" ------------------- |  (message)
  |<-- "New update" ------------------ |  (message)
  |-- Close frame --------------------|
  |<-- Close frame ------------------- |
  |         [Disconnected]             |
```

### Lifecycle Events
1. **Connect** — Handshake complete, connection open
2. **Open** — Ready to send/receive
3. **Message** — Data received
4. **Close** — Connection terminated
5. **Error** — Something went wrong

---

## 5.4 WebSocket Frames

| Frame Type | Purpose |
|-----------|---------|
| Text | Send string/JSON data |
| Binary | Send files, images, etc. |
| Ping | Check if connection is alive |
| Pong | Reply to Ping |
| Close | Signal connection close |

---

## 5.5 Implementing WebSockets in Django

### Step-by-Step Setup

**1. Install Dependencies**
```bash
pip install django channels channels-redis daphne
```

**2. Create Project & App**
```bash
django-admin startproject myproject
cd myproject
python manage.py startapp chat
```

**3. Settings (`settings.py`)**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'channels',   # Add this
    'chat',       # Your app
]

# ASGI Application
ASGI_APPLICATION = "myproject.asgi.application"

# Channel layer with Redis
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("127.0.0.1", 6379)],
        },
    },
}
```

**4. ASGI Configuration (`asgi.py`)**
```python
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
import chat.routing

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

application = ProtocolTypeRouter({
    "http": get_asgi_application(),        # Regular HTTP
    "websocket": AuthMiddlewareStack(      # WebSocket
        URLRouter(
            chat.routing.websocket_urlpatterns
        )
    ),
})
```

**5. Consumer (`chat/consumers.py`) — The WebSocket Handler**
```python
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):

    async def connect(self):
        """Called when client connects."""
        self.room_name = self.scope["url_route"]["kwargs"]["room_name"]
        self.room_group_name = f"chat_{self.room_name}"

        # Join the room group
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )
        await self.accept()  # Accept the connection

    async def disconnect(self, close_code):
        """Called when client disconnects."""
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    async def receive(self, text_data):
        """Called when a message is received from the client."""
        data = json.loads(text_data)
        message = data["message"]
        username = data.get("username", "Anonymous")

        # Broadcast to the group (all connected clients in this room)
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                "type": "chat_message",  # matches handler below
                "message": message,
                "username": username
            }
        )

    async def chat_message(self, event):
        """Handler for messages sent to the group."""
        await self.send(text_data=json.dumps({
            "message": event["message"],
            "username": event["username"]
        }))
```

**6. Routing (`chat/routing.py`)**
```python
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r"ws/chat/(?P<room_name>\w+)/$", consumers.ChatConsumer.as_asgi()),
]
```

**7. HTTP URLs (`chat/urls.py`)**
```python
from django.urls import path
from . import views

urlpatterns = [
    path("chat/<str:room_name>/", views.room_view, name="room"),
]
```

**8. View (`chat/views.py`)**
```python
from django.shortcuts import render

def room_view(request, room_name):
    return render(request, "chat/room.html", {"room_name": room_name})
```

**9. Template (`chat/templates/chat/room.html`)**
```html
<!DOCTYPE html>
<html>
<head><title>Chat - {{ room_name }}</title></head>
<body>
  <h2>Room: {{ room_name }}</h2>

  <div id="messages" style="height:300px; overflow-y:scroll; border:1px solid #ccc; padding:10px;"></div>

  <input id="message-input" type="text" placeholder="Type a message..." />
  <input id="username-input" type="text" placeholder="Your name" value="User" />
  <button onclick="sendMessage()">Send</button>

  <script>
    const roomName = "{{ room_name }}";
    const socket = new WebSocket(`ws://${window.location.host}/ws/chat/${roomName}/`);

    socket.onopen = function() {
      console.log("Connected!");
    };

    socket.onmessage = function(event) {
      const data = JSON.parse(event.data);
      const div = document.getElementById("messages");
      div.innerHTML += `<p><b>${data.username}:</b> ${data.message}</p>`;
      div.scrollTop = div.scrollHeight;
    };

    socket.onclose = function() {
      console.log("Disconnected!");
    };

    function sendMessage() {
      const message = document.getElementById("message-input").value;
      const username = document.getElementById("username-input").value;
      socket.send(JSON.stringify({ message, username }));
      document.getElementById("message-input").value = "";
    }
  </script>
</body>
</html>
```

**10. Run Redis**
```bash
# Using Docker
docker run -p 6379:6379 redis

# Or install Redis locally
sudo apt install redis-server
redis-server
```

**11. Run with Daphne (ASGI Server)**
```bash
daphne -p 8000 myproject.asgi:application
```

---

## 5.6 Routing in WebSockets

### URL Routing
```python
# chat/routing.py
websocket_urlpatterns = [
    re_path(r"ws/chat/(?P<room_name>\w+)/$", consumers.ChatConsumer.as_asgi()),
    re_path(r"ws/notifications/$", consumers.NotificationConsumer.as_asgi()),
]
```

### Channel Layers
- **Channel** — A unique name for one WebSocket connection
- **Group** — A named collection of channels (e.g., all users in "chat_room1")
- **Layer** — The backend (Redis) that manages groups

```python
# Add to group (on connect)
await self.channel_layer.group_add("group_name", self.channel_name)

# Remove from group (on disconnect)
await self.channel_layer.group_discard("group_name", self.channel_name)

# Send to whole group
await self.channel_layer.group_send("group_name", {"type": "my_handler", "data": "..."})

# Send to specific channel only
await self.channel_layer.send("specific_channel_name", {"type": "my_handler"})
```

---

## 5.7 Broadcasting Messages

### Group Communication (Chat Rooms)
```python
# Everyone in the room gets the message
await self.channel_layer.group_send(
    self.room_group_name,
    {
        "type": "chat_message",
        "message": message
    }
)

async def chat_message(self, event):
    await self.send(text_data=json.dumps({"message": event["message"]}))
```

### Sending from Outside (Django Views / Celery Tasks)
```python
# views.py or tasks.py — send to WebSocket clients from HTTP view
from channels.layers import get_channel_layer
from asgiref.sync import async_to_sync

channel_layer = get_channel_layer()

def notify_room(room_name, message):
    async_to_sync(channel_layer.group_send)(
        f"chat_{room_name}",
        {
            "type": "chat_message",
            "message": message
        }
    )
```

---

## 5.8 Scaling WebSockets

### Redis Channel Layer
- Allows **multiple Django instances** to share channel groups
- All instances connect to the same Redis, so messages reach all clients

```python
# settings.py
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("redis-server-host", 6379)],
        },
    },
}
```

### Horizontal Scaling
- Run multiple Daphne instances behind a load balancer
- Must use **sticky sessions** (same client always goes to same server) OR Redis channel layer

### Sticky Sessions (Nginx)
```nginx
upstream websocket_servers {
    ip_hash;  # sticky session by IP
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
}

server {
    location /ws/ {
        proxy_pass http://websocket_servers;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

---

## 5.9 Security in WebSockets

### Authentication (JWT/Session)
```python
# consumers.py
from channels.middleware import BaseMiddleware
from django.contrib.auth.models import AnonymousUser
from rest_framework_simplejwt.tokens import AccessToken
from django.contrib.auth import get_user_model

User = get_user_model()

class JWTAuthMiddleware(BaseMiddleware):
    async def __call__(self, scope, receive, send):
        query_string = scope.get("query_string", b"").decode()
        params = dict(p.split("=") for p in query_string.split("&") if "=" in p)
        token = params.get("token", "")

        try:
            access_token = AccessToken(token)
            scope["user"] = await User.objects.aget(id=access_token["user_id"])
        except Exception:
            scope["user"] = AnonymousUser()

        return await super().__call__(scope, receive, send)

# In consumer, check auth:
async def connect(self):
    if self.scope["user"].is_anonymous:
        await self.close()  # Reject unauthenticated users
        return
    await self.accept()
```

**Frontend: Connect with token**
```javascript
const token = localStorage.getItem("access_token");
const socket = new WebSocket(`ws://localhost:8000/ws/chat/room1/?token=${token}`);
```

### Origin Validation
```python
# settings.py
ALLOWED_HOSTS = ["yourdomain.com"]

# In consumer:
async def connect(self):
    origin = dict(self.scope["headers"]).get(b"origin", b"").decode()
    if "yourdomain.com" not in origin:
        await self.close()
        return
    await self.accept()
```

---

## 5.10 Error Handling

### Connection Drops
```python
async def disconnect(self, close_code):
    print(f"Client disconnected with code: {close_code}")
    await self.channel_layer.group_discard(
        self.room_group_name,
        self.channel_name
    )
```

### Reconnect Strategies (Frontend)
```javascript
function connectWebSocket() {
  const socket = new WebSocket("ws://localhost:8000/ws/chat/room1/");

  socket.onclose = function(event) {
    console.log("Disconnected. Reconnecting in 3 seconds...");
    setTimeout(connectWebSocket, 3000);  // auto-reconnect
  };

  socket.onerror = function(error) {
    console.error("WebSocket error:", error);
  };

  return socket;
}

let ws = connectWebSocket();
```

---

# 6. Comparison & Decision Making

## 6.1 Webhooks vs SSE vs WebSockets

| Feature | Webhooks | SSE | WebSockets |
|---------|----------|-----|------------|
| Direction | Server → Server | Server → Client | Bidirectional |
| Connection | Short-lived HTTP | Persistent HTTP | Persistent WS |
| Protocol | HTTP/HTTPS | HTTP/HTTPS | ws:// / wss:// |
| Use Case | Event notifications | Live updates | Chat, gaming |
| Binary Support | Yes (but uncommon) | No | Yes |
| Browser Needed | No | Yes | Yes |
| Auto-Reconnect | No (retry by sender) | Yes (built-in) | Manual |
| Complexity | Low | Low | Medium/High |
| Scaling | Easy | Medium | Harder |
| Django Setup | Simple (view) | Medium (streaming) | Complex (Channels) |

## 6.2 When to Use What

- **Webhooks** → External service needs to notify your server (Stripe, GitHub, PayPal)
- **SSE** → Server needs to push real-time updates to browser (live feed, notifications, progress)
- **WebSockets** → Two-way real-time communication (chat, games, collaboration tools)

**Decision Flowchart:**
```
Is the client a browser?
  NO  → Use Webhooks
  YES → Does the client need to SEND messages to server in real-time?
          NO  → Use SSE
          YES → Use WebSockets
```

---

# 7. Backend Architecture Patterns

## 7.1 Event-Driven Systems

### Simple Definition
Instead of components calling each other directly, they produce and consume **events**. A payment service emits "payment_received", and any interested service picks it up.

```
Payment Service
     |
     | (publishes "payment_received" event)
     v
Message Queue
     |
     |-- Email Service (sends confirmation)
     |-- Inventory Service (reduces stock)
     |-- Analytics Service (tracks purchase)
```

### Event Producers and Consumers
- **Producer** — Creates and publishes events
- **Consumer** — Subscribes to and processes events

---

## 7.2 Pub/Sub Systems

### Redis Pub/Sub
```python
import redis

r = redis.Redis()

# Publisher (in one process)
r.publish("channel_name", "Hello subscribers!")

# Subscriber (in another process)
pubsub = r.pubsub()
pubsub.subscribe("channel_name")
for message in pubsub.listen():
    if message["type"] == "message":
        print(message["data"])
```

### Kafka Basics
- More powerful than Redis Pub/Sub
- Persists messages, good for high throughput
- Used in large-scale production systems

---

## 7.3 Task Queues (Celery)

```bash
pip install celery redis
```

```python
# settings.py
CELERY_BROKER_URL = "redis://localhost:6379/0"
CELERY_RESULT_BACKEND = "redis://localhost:6379/0"

# celery.py (in project folder)
from celery import Celery
import os

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")
app = Celery("myproject")
app.config_from_object("django.conf:settings", namespace="CELERY")
app.autodiscover_tasks()

# tasks.py
from celery import shared_task

@shared_task
def send_email_task(user_id):
    # send email in background
    pass

# views.py - call it
send_email_task.delay(user.id)  # runs in background
```

**Run Celery Worker:**
```bash
celery -A myproject worker --loglevel=info
```

---

## 7.4 Microservices Communication

### Async Communication
- Services talk via **message queues** (Celery, Kafka, RabbitMQ)
- No direct API calls between services
- Services are decoupled

### Event Streaming
```
Order Service → publishes "order_created"
                     ↓
           Message Broker (Redis/Kafka)
                     ↓
Notification Service → sends confirmation email
Inventory Service   → updates stock
Billing Service     → creates invoice
```

---

# 8. Performance & Scaling

## 8.1 Load Handling

### Concurrent Connections
- Django (WSGI) handles one request at a time per worker
- Daphne / ASGI can handle thousands of concurrent WebSocket connections
- Use `uvicorn` or `daphne` for ASGI

### Throughput Optimization
- Use async consumers in Django Channels
- Minimize database queries in message handlers
- Use Redis for channel layers (not in-memory)

---

## 8.2 Caching Strategies

### Redis Caching
```python
# settings.py
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}

# views.py
from django.core.cache import cache

def get_data():
    data = cache.get("my_key")
    if not data:
        data = expensive_db_query()
        cache.set("my_key", data, timeout=300)  # cache 5 minutes
    return data
```

---

## 8.3 Horizontal Scaling

### Load Balancers
- Distribute incoming connections across multiple servers
- For WebSockets: use sticky sessions or Redis channel layer

### Stateless Design
- Don't store user state in server memory
- Use database or Redis for all shared state
- Any server should be able to handle any request

---

## 8.4 Monitoring

### Logging
```python
# settings.py
LOGGING = {
    "version": 1,
    "handlers": {
        "file": {
            "class": "logging.FileHandler",
            "filename": "app.log",
        },
    },
    "root": {
        "handlers": ["file"],
        "level": "INFO",
    },
}
```

### Metrics & Alerts
- Use **Sentry** for error tracking
- Use **Prometheus + Grafana** for metrics
- Use **Django Silk** for request profiling

---

# 9. Security Best Practices

## 9.1 Authentication

### API Keys
```python
# Middleware to check API key
class APIKeyMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        key = request.META.get("HTTP_X_API_KEY")
        if not key or key != settings.API_KEY:
            from django.http import JsonResponse
            return JsonResponse({"error": "Unauthorized"}, status=401)
        return self.get_response(request)
```

### JWT Tokens
```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path("api/token/", TokenObtainPairView.as_view()),
    path("api/token/refresh/", TokenRefreshView.as_view()),
]
```

---

## 9.2 Data Protection

### HTTPS
- Always use `wss://` (WebSocket Secure) in production, not `ws://`
- Set `SECURE_SSL_REDIRECT = True` in Django settings
- Use HTTPS for all webhook endpoints

### Encryption
- Sensitive data in transit: use HTTPS/WSS
- Sensitive data at rest: use Django's `EncryptedField` or `cryptography` library

---

## 9.3 Abuse Prevention

### Rate Limiting
```bash
pip install django-ratelimit
```

```python
from ratelimit.decorators import ratelimit

@ratelimit(key="ip", rate="10/m", block=True)
def my_view(request):
    ...
```

### Throttling in DRF
```python
# settings.py
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.AnonRateThrottle",
        "rest_framework.throttling.UserRateThrottle",
    ],
    "DEFAULT_THROTTLE_RATES": {
        "anon": "100/day",
        "user": "1000/day",
    },
}
```

---

## 9.4 Validation

### Input Validation
```python
from rest_framework import serializers

class MessageSerializer(serializers.Serializer):
    message = serializers.CharField(max_length=500)
    room = serializers.CharField(max_length=50)

# In consumer:
async def receive(self, text_data):
    data = json.loads(text_data)
    serializer = MessageSerializer(data=data)
    if not serializer.is_valid():
        await self.send(json.dumps({"error": serializer.errors}))
        return
    # process validated data
```

---

# 10. Advanced Topics

## 10.1 Async Programming in Python

### asyncio
```python
import asyncio

async def fetch_data():
    await asyncio.sleep(1)  # Non-blocking wait
    return "data"

async def main():
    result = await fetch_data()
    print(result)

asyncio.run(main())
```

### Event Loop
- The event loop runs all async tasks
- While one task waits (I/O), others can run
- This is how Django Channels handles thousands of connections

---

## 10.2 ASGI Deep Dive

### ASGI vs WSGI
| | WSGI | ASGI |
|---|------|------|
| Type | Synchronous | Asynchronous |
| Supports WebSockets | No | Yes |
| Supports SSE | Limited | Yes |
| Supports HTTP/2 | No | Yes |
| Django Support | Default | With Channels |

### ASGI Application Structure
```python
# asgi.py
application = ProtocolTypeRouter({
    "http": get_asgi_application(),    # handles HTTP
    "websocket": ...,                  # handles WS
    "lifespan": ...,                   # startup/shutdown events
})
```

### Lifespan Events
```python
# Run code on server startup/shutdown
from channels.routing import ProtocolTypeRouter

class LifespanApp:
    def __init__(self, scope):
        self.scope = scope

    async def __call__(self, receive, send):
        if self.scope["type"] == "lifespan":
            while True:
                message = await receive()
                if message["type"] == "lifespan.startup":
                    print("Server starting up...")
                    await send({"type": "lifespan.startup.complete"})
                elif message["type"] == "lifespan.shutdown":
                    print("Server shutting down...")
                    await send({"type": "lifespan.shutdown.complete"})
                    return
```

---

## 10.3 Real-Time Analytics

### Streaming Pipeline
```
User Action → WebSocket → Django → Redis Pub/Sub → Analytics Consumer → Dashboard
```

```python
# Publish event to analytics stream
async def receive(self, text_data):
    data = json.loads(text_data)

    # Publish to analytics channel
    await self.channel_layer.group_send(
        "analytics",
        {"type": "track_event", "event": data["event"], "user": str(self.scope["user"])}
    )
```

---

## 10.4 Fault Tolerance

### Circuit Breakers
- If a service keeps failing, **stop calling it** for a while
- Let it recover before trying again

```python
# Simple circuit breaker concept
class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failures = 0
        self.threshold = failure_threshold
        self.timeout = timeout
        self.last_failure_time = None
        self.open = False

    def call(self, func, *args, **kwargs):
        if self.open:
            if time.time() - self.last_failure_time > self.timeout:
                self.open = False  # Try again
            else:
                raise Exception("Circuit is open, service unavailable")

        try:
            result = func(*args, **kwargs)
            self.failures = 0
            return result
        except Exception as e:
            self.failures += 1
            self.last_failure_time = time.time()
            if self.failures >= self.threshold:
                self.open = True
            raise e
```

### Retry Strategies
```python
import time

def with_retry(func, max_retries=3, delay=1, backoff=2):
    for attempt in range(max_retries):
        try:
            return func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            wait = delay * (backoff ** attempt)
            print(f"Attempt {attempt + 1} failed. Retrying in {wait}s...")
            time.sleep(wait)
```

---

## 10.5 Distributed Systems Concepts

### CAP Theorem
You can only guarantee **2 of 3**:

| Property | Meaning |
|----------|---------|
| Consistency (C) | All nodes see the same data at the same time |
| Availability (A) | System always responds (even if data is stale) |
| Partition Tolerance (P) | System works even if some nodes can't communicate |

### Practical Choices
- **Redis** — CP (Consistency + Partition Tolerance)
- **Cassandra** — AP (Availability + Partition Tolerance)
- **Traditional RDBMS** — CA (Consistency + Availability)

---

# 11. Practical Projects

## 11.1 Beginner Projects

### Project 1: Webhook Receiver API

**Goal:** Build an endpoint that receives Stripe-like payment webhooks and stores them.

**Files needed:**
```
myproject/
  webhooks/
    models.py      # WebhookEvent model
    views.py       # @csrf_exempt POST endpoint
    urls.py        # path('webhook/', ...)
  settings.py
  urls.py
```

**Test with:**
```bash
curl -X POST http://localhost:8000/api/webhook/ \
  -H "Content-Type: application/json" \
  -d '{"event": "payment_success", "amount": 999}'
```

---

### Project 2: SSE Live Notification System

**Goal:** Show real-time notifications on a webpage without refresh.

```python
# views.py
def notification_stream(request):
    def generate():
        notifications = ["Welcome!", "New order", "Payment received"]
        for note in notifications:
            yield f"data: {json.dumps({'text': note})}\n\n"
            time.sleep(2)

    return StreamingHttpResponse(generate(), content_type="text/event-stream")
```

```javascript
// frontend
const source = new EventSource("/api/notifications/");
source.onmessage = (e) => {
  const data = JSON.parse(e.data);
  showNotification(data.text);
};
```

---

## 11.2 Intermediate Projects

### Project 3: Chat App Using WebSockets

**Features:**
- Multiple chat rooms
- Username display
- Message history

**Key files:**
```
chat/
  consumers.py    # ChatConsumer with connect/receive/disconnect
  routing.py      # WebSocket URL patterns
  models.py       # Message model for history
  views.py        # Room view
  templates/      # HTML with WebSocket JS
```

---

### Project 4: Real-Time Dashboard

**Goal:** Live updating dashboard showing server stats or orders.

**Architecture:**
```
Django Admin action → channel_layer.group_send() → WebSocket → Browser updates
```

```python
# Send update from anywhere
from channels.layers import get_channel_layer
from asgiref.sync import async_to_sync

def update_dashboard(data):
    layer = get_channel_layer()
    async_to_sync(layer.group_send)(
        "dashboard",
        {"type": "dashboard_update", "data": data}
    )
```

---

## 11.3 Advanced Projects

### Project 5: Scalable Notification System

**Stack:** Django + Channels + Redis + Celery

```
User action → HTTP API → Celery Task → Redis Pub/Sub → WebSocket Consumers → All connected browsers
```

### Project 6: Multi-Service Event-Driven Architecture

**Stack:** Multiple Django services communicating via Kafka/Redis

```
Service A (Orders) → publishes event → Redis/Kafka
Service B (Emails) → subscribes → sends confirmation
Service C (Inventory) → subscribes → updates stock
```

---

# 12. Tools & Libraries

## 12.1 Webhooks
| Tool | Purpose |
|------|---------|
| `requests` | Send HTTP requests (simulate webhook) |
| `djangorestframework` | Build clean API endpoints |
| `hmac` / `hashlib` | Signature verification |
| `ngrok` | Expose localhost to internet |
| `webhook.site` | Test webhooks online |

## 12.2 SSE
| Tool | Purpose |
|------|---------|
| `StreamingHttpResponse` | Django SSE response |
| `redis` | Pub/Sub for broadcasting |
| `EventSource` (JS) | Browser SSE client |

## 12.3 WebSockets
| Tool | Purpose |
|------|---------|
| `channels` | WebSocket support for Django |
| `channels-redis` | Redis channel layer |
| `daphne` | ASGI server for Django |
| `uvicorn` | Alternative ASGI server |
| `websockets` | Pure Python WebSocket library |

## 12.4 Supporting Tools
| Tool | Purpose |
|------|---------|
| `redis` | Pub/Sub, caching, channel layers |
| `celery` | Background task queue |
| `nginx` | Reverse proxy, load balancer |
| `docker` | Containerize Redis, services |
| `sentry` | Error tracking |
| `djangorestframework-simplejwt` | JWT authentication |

---

## Quick Reference: Full Project Setup Checklist

### Webhook Project
```
☐ pip install django djangorestframework
☐ Create app with webhook model
☐ @csrf_exempt POST view
☐ Signature verification
☐ Return 200 OK fast, process in background (Celery)
☐ Test with ngrok + curl
```

### SSE Project
```
☐ pip install django
☐ StreamingHttpResponse with text/event-stream
☐ Generator yielding "data: ...\n\n"
☐ Cache-Control: no-cache header
☐ EventSource in frontend
☐ Redis Pub/Sub for multi-client broadcasting
```

### WebSocket Project
```
☐ pip install django channels channels-redis daphne
☐ Add 'channels' to INSTALLED_APPS
☐ Set ASGI_APPLICATION in settings
☐ Configure CHANNEL_LAYERS with Redis
☐ Update asgi.py with ProtocolTypeRouter
☐ Create consumers.py with AsyncWebsocketConsumer
☐ Create routing.py with websocket_urlpatterns
☐ Run Redis: docker run -p 6379:6379 redis
☐ Run: daphne -p 8000 myproject.asgi:application
```
