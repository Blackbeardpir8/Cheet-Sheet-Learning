# HTTP Status Codes — Complete Reference

---

## Table of Contents

- [1xx — Informational](#1xx--informational)
  - [100 Continue](#100-continue)
  - [101 Switching Protocols](#101-switching-protocols)
  - [102 Processing](#102-processing)
  - [103 Early Hints](#103-early-hints)
- [2xx — Success](#2xx--success)
  - [200 OK](#200-ok)
  - [201 Created](#201-created)
  - [202 Accepted](#202-accepted)
  - [203 Non-Authoritative Information](#203-non-authoritative-information)
  - [204 No Content](#204-no-content)
  - [205 Reset Content](#205-reset-content)
  - [206 Partial Content](#206-partial-content)
  - [207 Multi-Status](#207-multi-status)
  - [208 Already Reported](#208-already-reported)
  - [226 IM Used](#226-im-used)
- [3xx — Redirection](#3xx--redirection)
  - [300 Multiple Choices](#300-multiple-choices)
  - [301 Moved Permanently](#301-moved-permanently)
  - [302 Found](#302-found)
  - [303 See Other](#303-see-other)
  - [304 Not Modified](#304-not-modified)
  - [305 Use Proxy](#305-use-proxy)
  - [307 Temporary Redirect](#307-temporary-redirect)
  - [308 Permanent Redirect](#308-permanent-redirect)
- [4xx — Client Errors](#4xx--client-errors)
  - [400 Bad Request](#400-bad-request)
  - [401 Unauthorized](#401-unauthorized)
  - [402 Payment Required](#402-payment-required)
  - [403 Forbidden](#403-forbidden)
  - [404 Not Found](#404-not-found)
  - [405 Method Not Allowed](#405-method-not-allowed)
  - [406 Not Acceptable](#406-not-acceptable)
  - [407 Proxy Authentication Required](#407-proxy-authentication-required)
  - [408 Request Timeout](#408-request-timeout)
  - [409 Conflict](#409-conflict)
  - [410 Gone](#410-gone)
  - [411 Length Required](#411-length-required)
  - [412 Precondition Failed](#412-precondition-failed)
  - [413 Content Too Large](#413-content-too-large)
  - [414 URI Too Long](#414-uri-too-long)
  - [415 Unsupported Media Type](#415-unsupported-media-type)
  - [416 Range Not Satisfiable](#416-range-not-satisfiable)
  - [417 Expectation Failed](#417-expectation-failed)
  - [418 I'm a Teapot](#418-im-a-teapot)
  - [421 Misdirected Request](#421-misdirected-request)
  - [422 Unprocessable Content](#422-unprocessable-content)
  - [423 Locked](#423-locked)
  - [424 Failed Dependency](#424-failed-dependency)
  - [425 Too Early](#425-too-early)
  - [426 Upgrade Required](#426-upgrade-required)
  - [428 Precondition Required](#428-precondition-required)
  - [429 Too Many Requests](#429-too-many-requests)
  - [431 Request Header Fields Too Large](#431-request-header-fields-too-large)
  - [451 Unavailable For Legal Reasons](#451-unavailable-for-legal-reasons)
- [5xx — Server Errors](#5xx--server-errors)
  - [500 Internal Server Error](#500-internal-server-error)
  - [501 Not Implemented](#501-not-implemented)
  - [502 Bad Gateway](#502-bad-gateway)
  - [503 Service Unavailable](#503-service-unavailable)
  - [504 Gateway Timeout](#504-gateway-timeout)
  - [505 HTTP Version Not Supported](#505-http-version-not-supported)
  - [506 Variant Also Negotiates](#506-variant-also-negotiates)
  - [507 Insufficient Storage](#507-insufficient-storage)
  - [508 Loop Detected](#508-loop-detected)
  - [510 Not Extended](#510-not-extended)
  - [511 Network Authentication Required](#511-network-authentication-required)
- [DRF Status Code Usage Summary](#drf-status-code-usage-summary)

---

## 1xx — Informational

> The server received the request and is continuing to process it. These are provisional responses.

---

### 100 Continue

- **Meaning:** The server has received the request headers and the client should proceed to send the request body.
- **When it happens:** Client sends a large request and first checks if the server will accept it.
- **Real-world example:** Uploading a large file — the client asks "Can I send this?" before actually sending it.
- **DRF use:** Not used directly in DRF.

```http
HTTP/1.1 100 Continue
```

---

### 101 Switching Protocols

- **Meaning:** The server agrees to switch to a different protocol requested by the client (e.g., HTTP → WebSocket).
- **When it happens:** When a client requests a protocol upgrade using the `Upgrade` header.
- **Real-world example:** Starting a WebSocket connection for a live chat app.
- **DRF use:** Used with Django Channels for WebSocket support.

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
```

---

### 102 Processing

- **Meaning:** The server has received and is processing the request, but no response is available yet.
- **When it happens:** Long-running requests (WebDAV).
- **Real-world example:** Server is processing a complex database operation and wants to tell the client "I'm still working."
- **DRF use:** Not commonly used in DRF.

```http
HTTP/1.1 102 Processing
```

---

### 103 Early Hints

- **Meaning:** Allows the server to send some response headers before the final HTTP message.
- **When it happens:** Used to preload resources while the server prepares the response.
- **Real-world example:** Server tells the browser to start loading CSS/JS while the HTML is still being generated.
- **DRF use:** Not applicable in DRF APIs.

```http
HTTP/1.1 103 Early Hints
Link: </style.css>; rel=preload; as=style
```

---

## 2xx — Success

> The request was successfully received, understood, and accepted.

---

### 200 OK

- **Meaning:** The request succeeded. This is the standard success response.
- **When it happens:** Successful GET, PUT, PATCH, DELETE requests.
- **Real-world example:** You open a webpage — the server sends 200 with the HTML content.
- **DRF use:** Default success response. Used for GET (retrieve/list), PUT, PATCH.

```python
from rest_framework.response import Response
from rest_framework import status

def get(self, request):
    data = {"message": "Success", "users": [...]}
    return Response(data, status=status.HTTP_200_OK)
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"id": 1, "name": "John"}
```

---

### 201 Created

- **Meaning:** The request succeeded and a new resource was created as a result.
- **When it happens:** After a successful POST request that creates a new resource.
- **Real-world example:** You register a new account — the server responds with 201 and your new user data.
- **DRF use:** Used after creating a new object. `CreateModelMixin` returns 201 automatically.

```python
def post(self, request):
    serializer = UserSerializer(data=request.data)
    if serializer.is_valid():
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)
    return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

```http
HTTP/1.1 201 Created
Location: /api/users/42/

{"id": 42, "name": "John", "email": "john@example.com"}
```

---

### 202 Accepted

- **Meaning:** The request has been accepted for processing, but processing is NOT complete yet.
- **When it happens:** Asynchronous operations — the server queues the task and responds immediately.
- **Real-world example:** You submit a video for processing — the server accepts it and says "We'll process it, check back later."
- **DRF use:** Used when handing off tasks to Celery or background workers.

```python
from myapp.tasks import send_bulk_email

def post(self, request):
    send_bulk_email.delay(request.data)
    return Response(
        {"message": "Email task queued. Will be sent shortly."},
        status=status.HTTP_202_ACCEPTED
    )
```

```http
HTTP/1.1 202 Accepted

{"message": "Task accepted and queued for processing."}
```

---

### 203 Non-Authoritative Information

- **Meaning:** The returned metadata is NOT from the origin server but from a proxy or local copy.
- **When it happens:** When a proxy modifies or adds headers before forwarding the response.
- **Real-world example:** A CDN returns slightly modified response headers that differ from the origin server's.
- **DRF use:** Rarely used directly in DRF.

```http
HTTP/1.1 203 Non-Authoritative Information
```

---

### 204 No Content

- **Meaning:** The request succeeded but there is NO content to return in the response body.
- **When it happens:** After a successful DELETE or when an update needs no data back.
- **Real-world example:** You delete a post — the server deletes it and returns nothing (no body needed).
- **DRF use:** Standard response for DELETE operations.

```python
def delete(self, request, pk):
    obj = get_object_or_404(MyModel, pk=pk)
    obj.delete()
    return Response(status=status.HTTP_204_NO_CONTENT)
```

```http
HTTP/1.1 204 No Content
```

> **Note:** 204 responses must NEVER include a response body.

---

### 205 Reset Content

- **Meaning:** The request succeeded. The client should reset the document view (e.g., clear a form).
- **When it happens:** After submitting a form, telling the browser to reset it.
- **Real-world example:** After submitting a feedback form, the server tells the browser to clear all fields.
- **DRF use:** Rarely used in REST APIs.

```http
HTTP/1.1 205 Reset Content
```

---

### 206 Partial Content

- **Meaning:** The server is delivering only part of the resource as requested by the client using a `Range` header.
- **When it happens:** Video streaming, resumable downloads, large file transfers.
- **Real-world example:** Netflix sends only the portion of a video you're currently watching, not the entire file.
- **DRF use:** Used in file download/streaming views.

```http
HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1023/146515
Content-Length: 1024
Content-Type: video/mp4
```

---

### 207 Multi-Status

- **Meaning:** Conveys information about multiple resources in a single response (WebDAV).
- **When it happens:** Batch operations where multiple resources have different outcomes.
- **Real-world example:** Bulk API — 3 records created, 1 failed — each with its own status.
- **DRF use:** Used in custom bulk operation views.

```http
HTTP/1.1 207 Multi-Status
Content-Type: application/xml

<multistatus>
  <response><status>200 OK</status></response>
  <response><status>404 Not Found</status></response>
</multistatus>
```

---

### 208 Already Reported

- **Meaning:** The members of a DAV binding have already been enumerated in a previous reply and are not being included again.
- **When it happens:** WebDAV — avoids repeating already-reported resource info in a multi-status response.
- **Real-world example:** A file in multiple folders — no need to report it twice.
- **DRF use:** Not applicable in standard DRF.

```http
HTTP/1.1 208 Already Reported
```

---

### 226 IM Used

- **Meaning:** The server fulfilled a GET request and the response represents the result of one or more instance-manipulations applied to the current instance.
- **When it happens:** Delta encoding — server sends only what changed, not the full resource.
- **Real-world example:** Instead of resending an entire JSON object, the server sends only the diff/patch.
- **DRF use:** Not applicable in standard DRF.

```http
HTTP/1.1 226 IM Used
```

---

## 3xx — Redirection

> The client must take additional action to complete the request (usually follow a redirect).

---

### 300 Multiple Choices

- **Meaning:** The request has more than one possible response. The client should choose one.
- **When it happens:** When a resource is available in multiple formats (e.g., JSON or XML).
- **Real-world example:** A document available in English and French — server lists both options.
- **DRF use:** Not used in typical DRF APIs.

```http
HTTP/1.1 300 Multiple Choices
```

---

### 301 Moved Permanently

- **Meaning:** The resource has moved to a new URL permanently. All future requests should use the new URL.
- **When it happens:** Website domain changes, URL restructuring.
- **Real-world example:** `http://` automatically redirects to `https://` permanently.
- **DRF use:** Django's `HttpResponsePermanentRedirect`.

```python
from django.http import HttpResponsePermanentRedirect

def old_endpoint(request):
    return HttpResponsePermanentRedirect('/api/v2/users/')
```

```http
HTTP/1.1 301 Moved Permanently
Location: https://newsite.com/api/users/
```

---

### 302 Found

- **Meaning:** The resource is temporarily located at a different URI. Use the original URL for future requests.
- **When it happens:** Temporary maintenance pages, short-term redirects.
- **Real-world example:** A page under maintenance redirects users to a "coming soon" page temporarily.
- **DRF use:** Django's `redirect()` shortcut or `HttpResponseRedirect`.

```python
from django.shortcuts import redirect

def my_view(request):
    return redirect('/api/v1/users/')
```

```http
HTTP/1.1 302 Found
Location: /maintenance.html
```

---

### 303 See Other

- **Meaning:** The server sends this to direct the client to retrieve the response at another URI using a GET request.
- **When it happens:** After a POST, redirect the client to a GET endpoint to show the result.
- **Real-world example:** After submitting a form (POST), redirect to a success page (GET).
- **DRF use:** Post-Redirect-Get pattern.

```http
HTTP/1.1 303 See Other
Location: /api/orders/success/
```

---

### 304 Not Modified

- **Meaning:** The client's cached version is still up to date. No need to resend the resource.
- **When it happens:** Client sends `If-Modified-Since` or `ETag` header; server checks if content changed.
- **Real-world example:** Browser caches a logo image — next visit, server says "nothing changed, use your cache."
- **DRF use:** Handled automatically via conditional request headers.

```http
HTTP/1.1 304 Not Modified
ETag: "abc123"
```

---

### 305 Use Proxy

- **Meaning:** The requested resource must be accessed through a specified proxy.
- **When it happens:** The origin server requires proxy access.
- **DRF use:** Deprecated. Not used in modern APIs.
- **Note:** Deprecated in HTTP/1.1 due to security concerns.

```http
HTTP/1.1 305 Use Proxy
Location: http://proxy.example.com
```

---

### 307 Temporary Redirect

- **Meaning:** Temporary redirect — the client MUST use the SAME HTTP method when following the redirect.
- **When it happens:** Temporary redirect where the HTTP method must be preserved (unlike 302).
- **Real-world example:** A POST to `/api/orders/` is temporarily redirected to `/api/v2/orders/` — still uses POST.
- **DRF use:** Not typically used directly in DRF.

```http
HTTP/1.1 307 Temporary Redirect
Location: /api/v2/users/
```

> **Key difference from 302:** 302 allows changing the method to GET; 307 keeps the original method.

---

### 308 Permanent Redirect

- **Meaning:** Permanent redirect — the client MUST use the SAME HTTP method. Like 301 but method is preserved.
- **When it happens:** Permanent URL restructure where POST/PUT method must be kept.
- **Real-world example:** API version migration — POST to v1 permanently moves to v2, still as POST.
- **DRF use:** Not typically used directly in DRF.

```http
HTTP/1.1 308 Permanent Redirect
Location: /api/v2/users/
```

> **Key difference from 301:** 301 allows changing the method to GET; 308 keeps the original method.

---

## 4xx — Client Errors

> The client made a mistake. The server understood the request but cannot process it.

---

### 400 Bad Request

- **Meaning:** The server cannot process the request because of malformed syntax, invalid data, or missing fields.
- **When it happens:** Invalid JSON, missing required fields, wrong data types.
- **Real-world example:** You submit a form without filling in required fields.
- **DRF use:** Automatically returned when `serializer.is_valid()` fails (via `ValidationError`).

```python
def post(self, request):
    serializer = UserSerializer(data=request.data)
    if not serializer.is_valid():
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

```json
// Response body
{
  "email": ["This field is required."],
  "age": ["A valid integer is required."]
}
```

---

### 401 Unauthorized

- **Meaning:** The request requires authentication. The client has NOT provided valid credentials.
- **When it happens:** Accessing a protected endpoint without a token or with an expired token.
- **Real-world example:** Trying to view your account dashboard without being logged in.
- **DRF use:** Raised automatically when authentication fails (`NotAuthenticated` exception).

```python
from rest_framework.permissions import IsAuthenticated

class ProfileView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({"user": request.user.username})
```

```json
// 401 Response
{
  "detail": "Authentication credentials were not provided."
}
```

> **Note:** 401 = "You need to log in." | 403 = "You're logged in but not allowed."

---

### 402 Payment Required

- **Meaning:** Reserved for future use. Indicates the client needs to make a payment.
- **When it happens:** Subscription-based APIs, premium feature access.
- **Real-world example:** Hitting a paid API endpoint without an active subscription.
- **DRF use:** Custom implementation for billing/subscription systems.

```python
def get(self, request):
    if not request.user.has_active_subscription:
        return Response(
            {"error": "Subscription required to access this feature."},
            status=status.HTTP_402_PAYMENT_REQUIRED
        )
```

---

### 403 Forbidden

- **Meaning:** The server understood the request, the client is authenticated, but does NOT have permission.
- **When it happens:** Logged-in user tries to access another user's data or an admin-only endpoint.
- **Real-world example:** A regular user tries to delete someone else's account.
- **DRF use:** Raised automatically by DRF permission classes (`PermissionDenied` exception).

```python
from rest_framework.permissions import IsAdminUser

class AdminDashboardView(APIView):
    permission_classes = [IsAdminUser]

    def get(self, request):
        return Response({"data": "Admin only data"})
```

```json
// 403 Response
{
  "detail": "You do not have permission to perform this action."
}
```

---

### 404 Not Found

- **Meaning:** The server cannot find the requested resource. The URL does not exist.
- **When it happens:** Wrong URL, deleted resource, typo in the endpoint.
- **Real-world example:** Visiting a product page for a product that was removed.
- **DRF use:** Raised via `get_object_or_404()` or `NotFound` exception.

```python
from rest_framework.exceptions import NotFound
from django.shortcuts import get_object_or_404

def get(self, request, pk):
    # Option 1
    obj = get_object_or_404(Product, pk=pk)

    # Option 2
    try:
        obj = Product.objects.get(pk=pk)
    except Product.DoesNotExist:
        raise NotFound(detail="Product not found.")

    return Response(ProductSerializer(obj).data)
```

```json
// 404 Response
{
  "detail": "Not found."
}
```

---

### 405 Method Not Allowed

- **Meaning:** The HTTP method used is not supported for this endpoint.
- **When it happens:** Sending a DELETE request to an endpoint that only supports GET and POST.
- **Real-world example:** Trying to DELETE `/api/users/` (list endpoint) which doesn't allow deletion.
- **DRF use:** Raised automatically by DRF when the method is not defined in the view.

```python
class UserListView(APIView):
    def get(self, request):      # GET is allowed
        pass
    def post(self, request):     # POST is allowed
        pass
    # DELETE not defined → DRF returns 405 automatically
```

```json
// 405 Response
{
  "detail": "Method \"DELETE\" not allowed."
}
```

---

### 406 Not Acceptable

- **Meaning:** The server cannot produce a response matching the list of acceptable values in the request's `Accept` headers.
- **When it happens:** Client asks for XML but the server only supports JSON.
- **Real-world example:** Client sends `Accept: application/xml` but the API only returns JSON.
- **DRF use:** Raised automatically during content negotiation.

```http
GET /api/users/ HTTP/1.1
Accept: application/xml

HTTP/1.1 406 Not Acceptable
```

---

### 407 Proxy Authentication Required

- **Meaning:** The client must authenticate itself with the proxy before access is allowed.
- **When it happens:** Corporate networks with authentication-required proxies.
- **Real-world example:** Office network requires proxy login before accessing external APIs.
- **DRF use:** Not used in DRF directly. Handled at the network/infrastructure level.

```http
HTTP/1.1 407 Proxy Authentication Required
Proxy-Authenticate: Basic realm="proxy"
```

---

### 408 Request Timeout

- **Meaning:** The server timed out waiting for the request. The client didn't send a complete request in time.
- **When it happens:** Slow network, client took too long to send data.
- **Real-world example:** You started uploading a file but your connection was too slow and the server gave up.
- **DRF use:** Handled by the web server (nginx/gunicorn), not DRF itself.

```http
HTTP/1.1 408 Request Timeout
```

---

### 409 Conflict

- **Meaning:** The request conflicts with the current state of the resource.
- **When it happens:** Duplicate unique field (email already exists), concurrent update conflicts.
- **Real-world example:** Trying to register with an email that's already in use.
- **DRF use:** Returned manually for conflicts like duplicate entries.

```python
from django.db import IntegrityError

def post(self, request):
    try:
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
    except IntegrityError:
        return Response(
            {"error": "A user with this email already exists."},
            status=status.HTTP_409_CONFLICT
        )
```

```json
// 409 Response
{
  "error": "A user with this email already exists."
}
```

---

### 410 Gone

- **Meaning:** The resource has been permanently deleted and will NOT be available again.
- **When it happens:** A resource that has been permanently removed (stronger than 404).
- **Real-world example:** A user account that was permanently deleted and will never come back.
- **DRF use:** Custom use to differentiate "never existed" (404) vs "permanently deleted" (410).

```python
def get(self, request, pk):
    if DeletedRecord.objects.filter(original_pk=pk).exists():
        return Response(
            {"detail": "This resource has been permanently deleted."},
            status=status.HTTP_410_GONE
        )
```

---

### 411 Length Required

- **Meaning:** The server refuses to accept the request without a defined `Content-Length` header.
- **When it happens:** Server requires knowing the size of the request body.
- **DRF use:** Handled by the web server, not DRF.

```http
HTTP/1.1 411 Length Required
```

---

### 412 Precondition Failed

- **Meaning:** One or more conditions given in the request headers were evaluated and found to be false.
- **When it happens:** Conditional requests with `If-Match` or `If-Unmodified-Since` that fail.
- **Real-world example:** Optimistic locking — trying to update a record that was already changed by someone else.
- **DRF use:** Rarely used directly in DRF.

```http
HTTP/1.1 412 Precondition Failed
```

---

### 413 Content Too Large

- **Meaning:** The request body is larger than the server is willing or able to process.
- **When it happens:** Uploading a file that exceeds the size limit.
- **Real-world example:** Uploading a 500MB video when the API only allows 10MB.
- **DRF use:** Handled via Django settings.

```python
# settings.py
DATA_UPLOAD_MAX_MEMORY_SIZE = 10 * 1024 * 1024  # 10 MB limit
FILE_UPLOAD_MAX_MEMORY_SIZE = 10 * 1024 * 1024  # 10 MB limit
```

---

### 414 URI Too Long

- **Meaning:** The URI provided was too long for the server to process.
- **When it happens:** Extremely long query strings (e.g., hundreds of filter parameters in a GET request).
- **Real-world example:** A search query URL that is thousands of characters long.
- **DRF use:** Handled by the web server (nginx).

```http
HTTP/1.1 414 URI Too Long
```

---

### 415 Unsupported Media Type

- **Meaning:** The request's `Content-Type` is not supported by the server.
- **When it happens:** Sending XML to an API that only accepts JSON.
- **Real-world example:** Sending `Content-Type: text/plain` to an API expecting `application/json`.
- **DRF use:** Raised automatically by DRF during content negotiation.

```http
POST /api/users/ HTTP/1.1
Content-Type: text/plain

HTTP/1.1 415 Unsupported Media Type
```

```json
// DRF Response
{
  "detail": "Unsupported media type \"text/plain\" in request."
}
```

---

### 416 Range Not Satisfiable

- **Meaning:** The server cannot serve the requested byte range.
- **When it happens:** Client requests a range that doesn't exist in the file (e.g., bytes 9000-9999 of a 100-byte file).
- **Real-world example:** Resuming a download at a byte offset that's past the end of the file.
- **DRF use:** Not commonly used in DRF REST APIs.

```http
HTTP/1.1 416 Range Not Satisfiable
Content-Range: bytes */1234
```

---

### 417 Expectation Failed

- **Meaning:** The server cannot meet the requirements specified in the `Expect` request header.
- **When it happens:** Client sends `Expect: 100-continue` but server says it can't comply.
- **DRF use:** Not used in DRF directly.

```http
HTTP/1.1 417 Expectation Failed
```

---

### 418 I'm a Teapot

- **Meaning:** The server refuses to brew coffee because it is a teapot. (April Fools' joke — RFC 2324).
- **When it happens:** Never seriously, but some APIs use it for fun or to handle silly requests.
- **Real-world example:** Google had a teapot Easter egg. Some APIs return it for intentionally wrong requests.
- **DRF use:** Fun custom use only.

```python
def get(self, request):
    return Response(
        {"error": "I'm a teapot. I cannot brew coffee."},
        status=418
    )
```

---

### 421 Misdirected Request

- **Meaning:** The request was directed at a server that is unable to produce a response.
- **When it happens:** Request sent to a server that is not configured to respond to this combination of scheme and authority.
- **DRF use:** Not applicable in standard DRF setups.

```http
HTTP/1.1 421 Misdirected Request
```

---

### 422 Unprocessable Content

- **Meaning:** The request is well-formed (valid syntax) but contains semantic errors (invalid values).
- **When it happens:** A date field has valid JSON but an impossible date like `"2024-13-45"`.
- **Real-world example:** Submitting an age of `-5` — technically valid JSON, but semantically invalid.
- **DRF use:** Alternative to 400 for semantic validation errors.

```python
from rest_framework import serializers

class AgeValidator:
    def __call__(self, value):
        if value < 0:
            raise serializers.ValidationError("Age cannot be negative.")
        # DRF returns 400 by default; return 422 manually if preferred

def post(self, request):
    return Response(
        {"age": "Age must be a positive number."},
        status=status.HTTP_422_UNPROCESSABLE_ENTITY
    )
```

---

### 423 Locked

- **Meaning:** The resource being accessed is locked (WebDAV).
- **When it happens:** Another process/user has exclusively locked the resource.
- **Real-world example:** A document being edited by another user in a collaborative app.
- **DRF use:** Not typically used in DRF.

```http
HTTP/1.1 423 Locked
```

---

### 424 Failed Dependency

- **Meaning:** The method could not be performed because the action depended on another action that failed (WebDAV).
- **When it happens:** Batch operations where one step depends on a previous step that failed.
- **DRF use:** Not applicable in standard DRF.

```http
HTTP/1.1 424 Failed Dependency
```

---

### 425 Too Early

- **Meaning:** The server is unwilling to risk processing a request that might be replayed.
- **When it happens:** TLS 1.3 early data scenarios — prevents replay attacks.
- **DRF use:** Not used in DRF directly.

```http
HTTP/1.1 425 Too Early
```

---

### 426 Upgrade Required

- **Meaning:** The server refuses to perform the request using the current protocol. Client must upgrade.
- **When it happens:** Server requires TLS (HTTPS) but client is using HTTP.
- **Real-world example:** API that requires HTTPS and rejects plain HTTP connections.
- **DRF use:** Not used in DRF directly (handled at server level).

```http
HTTP/1.1 426 Upgrade Required
Upgrade: HTTPS/1.3
```

---

### 428 Precondition Required

- **Meaning:** The origin server requires the request to be conditional.
- **When it happens:** Server wants to prevent "lost update" problems — requires `If-Match` header.
- **Real-world example:** Updating a record requires proving you have the latest version (using ETag).
- **DRF use:** Not commonly used in DRF.

```http
HTTP/1.1 428 Precondition Required

{"message": "Request must be conditional. Provide If-Match header."}
```

---

### 429 Too Many Requests

- **Meaning:** The client has sent too many requests in a given amount of time (rate limiting).
- **When it happens:** User hits the API too frequently — their requests are throttled.
- **Real-world example:** A user calling an API 1000 times per minute when the limit is 100/hour.
- **DRF use:** Raised automatically by DRF's throttling classes.

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

```json
// 429 Response
{
  "detail": "Request was throttled. Expected available in 86400 seconds."
}
```

---

### 431 Request Header Fields Too Large

- **Meaning:** The server is unwilling to process the request because its headers are too large.
- **When it happens:** Too many cookies, oversized Authorization tokens.
- **Real-world example:** A JWT token that is extremely large causes this error.
- **DRF use:** Handled by the web server (nginx), not DRF.

```http
HTTP/1.1 431 Request Header Fields Too Large
```

---

### 451 Unavailable For Legal Reasons

- **Meaning:** The server is denying access to the resource as a consequence of a legal demand.
- **When it happens:** DMCA takedown, court orders, government censorship.
- **Real-world example:** A song that's been taken down due to a copyright claim.
- **DRF use:** Custom use for legal compliance.

```python
def get(self, request, pk):
    return Response(
        {"detail": "This resource is unavailable due to a legal order."},
        status=status.HTTP_451_UNAVAILABLE_FOR_LEGAL_REASONS
    )
```

---

## 5xx — Server Errors

> The server failed to fulfill a valid request. The mistake is on the server side.

---

### 500 Internal Server Error

- **Meaning:** The server encountered an unexpected error and couldn't complete the request.
- **When it happens:** Unhandled exceptions, bugs in code, database connection failures.
- **Real-world example:** A divide-by-zero error in your view code causes the server to crash.
- **DRF use:** Raised automatically when an unhandled exception occurs. DRF catches it and returns 500.

```python
# To handle globally in DRF — settings.py
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'myapp.exceptions.custom_exception_handler'
}

# myapp/exceptions.py
from rest_framework.views import exception_handler
from rest_framework.response import Response
from rest_framework import status

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)
    if response is None:
        return Response(
            {"error": "An unexpected server error occurred."},
            status=status.HTTP_500_INTERNAL_SERVER_ERROR
        )
    return response
```

```json
// 500 Response (DRF default in production)
{
  "detail": "Internal server error."
}
```

---

### 501 Not Implemented

- **Meaning:** The server does not support the functionality required to fulfill the request.
- **When it happens:** Calling an HTTP method that the server doesn't recognize or support.
- **Real-world example:** Using a new HTTP method that the server doesn't know about.
- **DRF use:** Not common; can be raised manually for unimplemented features.

```python
def patch(self, request, pk):
    return Response(
        {"detail": "PATCH method not yet implemented."},
        status=status.HTTP_501_NOT_IMPLEMENTED
    )
```

---

### 502 Bad Gateway

- **Meaning:** The server, while acting as a gateway/proxy, received an invalid response from the upstream server.
- **When it happens:** nginx → Django communication fails, upstream service is down.
- **Real-world example:** Your Django app crashes and nginx can't connect to it — users see 502.
- **DRF use:** Handled by nginx/proxy configuration. Not DRF's responsibility.

```nginx
# nginx error when Django (gunicorn) is down
upstream django {
    server 127.0.0.1:8000;
}
# If gunicorn crashes → nginx returns 502
```

---

### 503 Service Unavailable

- **Meaning:** The server is temporarily unable to handle the request (overloaded or down for maintenance).
- **When it happens:** Server maintenance, too many concurrent requests, server overload.
- **Real-world example:** A website shows "We'll be back shortly" during scheduled maintenance.
- **DRF use:** Can be returned via custom middleware for maintenance mode.

```python
# myapp/middleware.py
class MaintenanceModeMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        from django.conf import settings
        if getattr(settings, 'MAINTENANCE_MODE', False):
            from django.http import JsonResponse
            return JsonResponse(
                {"error": "Service under maintenance. Please try again later."},
                status=503
            )
        return self.get_response(request)
```

```json
// 503 Response
{
  "error": "Service under maintenance. Please try again later."
}
```

---

### 504 Gateway Timeout

- **Meaning:** The server, acting as a gateway/proxy, didn't receive a timely response from the upstream server.
- **When it happens:** Upstream service (database, third-party API) is too slow to respond.
- **Real-world example:** Your Django app calls an external payment API that takes 60 seconds — nginx times out at 30 seconds.
- **DRF use:** Handled at the nginx/proxy level. Tune timeout settings.

```nginx
# nginx.conf — increase proxy timeout
proxy_read_timeout 60s;
proxy_connect_timeout 60s;
```

---

### 505 HTTP Version Not Supported

- **Meaning:** The HTTP version used in the request is not supported by the server.
- **When it happens:** Client uses HTTP/3 but server only supports HTTP/1.1.
- **DRF use:** Not applicable in DRF.

```http
HTTP/1.1 505 HTTP Version Not Supported
```

---

### 506 Variant Also Negotiates

- **Meaning:** The chosen variant is itself configured to engage in content negotiation — causing a circular reference.
- **When it happens:** Misconfigured content negotiation on the server.
- **DRF use:** Not applicable in DRF.

```http
HTTP/1.1 506 Variant Also Negotiates
```

---

### 507 Insufficient Storage

- **Meaning:** The server cannot store the representation needed to complete the request (WebDAV).
- **When it happens:** Server disk is full and can't save the uploaded file or data.
- **Real-world example:** User uploads a file but the server has no disk space left.
- **DRF use:** Custom use for file upload views when disk space is exhausted.

```python
import os
import shutil

def post(self, request):
    total, used, free = shutil.disk_usage("/")
    if free < 1024 * 1024 * 100:  # Less than 100MB free
        return Response(
            {"error": "Server storage is full. Contact administrator."},
            status=status.HTTP_507_INSUFFICIENT_STORAGE
        )
```

---

### 508 Loop Detected

- **Meaning:** The server detected an infinite loop while processing the request (WebDAV).
- **When it happens:** A circular reference in directory structures (WebDAV).
- **DRF use:** Not applicable in standard DRF.

```http
HTTP/1.1 508 Loop Detected
```

---

### 510 Not Extended

- **Meaning:** Further extensions to the request are required for the server to fulfill it.
- **When it happens:** The server needs additional extensions/information to process the request.
- **DRF use:** Not applicable in DRF.

```http
HTTP/1.1 510 Not Extended
```

---

### 511 Network Authentication Required

- **Meaning:** The client needs to authenticate to gain network access (not web server authentication).
- **When it happens:** Captive portals — hotel WiFi login pages, airport internet access.
- **Real-world example:** Connecting to hotel WiFi — browser redirected to login page before internet access.
- **DRF use:** Not used in DRF directly.

```http
HTTP/1.1 511 Network Authentication Required
```

---

## DRF Status Code Usage Summary

> Quick reference for the most commonly used status codes in Django REST Framework.

### Most Common in DRF

| Status Code | Name | When to Use | DRF Constant |
|-------------|------|-------------|--------------|
| `200` | OK | Successful GET, PUT, PATCH | `status.HTTP_200_OK` |
| `201` | Created | Successful POST (new resource) | `status.HTTP_201_CREATED` |
| `202` | Accepted | Async/background task queued | `status.HTTP_202_ACCEPTED` |
| `204` | No Content | Successful DELETE | `status.HTTP_204_NO_CONTENT` |
| `400` | Bad Request | Invalid input / validation error | `status.HTTP_400_BAD_REQUEST` |
| `401` | Unauthorized | Not authenticated | `status.HTTP_401_UNAUTHORIZED` |
| `403` | Forbidden | Authenticated but no permission | `status.HTTP_403_FORBIDDEN` |
| `404` | Not Found | Resource doesn't exist | `status.HTTP_404_NOT_FOUND` |
| `405` | Method Not Allowed | HTTP method not supported | `status.HTTP_405_METHOD_NOT_ALLOWED` |
| `409` | Conflict | Duplicate/conflict (e.g., email exists) | `status.HTTP_409_CONFLICT` |
| `422` | Unprocessable Content | Semantic validation error | `status.HTTP_422_UNPROCESSABLE_ENTITY` |
| `429` | Too Many Requests | Rate limit exceeded | `status.HTTP_429_TOO_MANY_REQUESTS` |
| `500` | Internal Server Error | Unhandled server exception | `status.HTTP_500_INTERNAL_SERVER_ERROR` |
| `503` | Service Unavailable | Server maintenance/overload | `status.HTTP_503_SERVICE_UNAVAILABLE` |

### How to Use DRF Status Constants

```python
from rest_framework import status
from rest_framework.response import Response

# Always use DRF constants instead of raw numbers
return Response(data, status=status.HTTP_200_OK)         # Good
return Response(data, status=200)                         # Works but not recommended

# All available constants follow this naming pattern:
# status.HTTP_<CODE>_<NAME_IN_CAPS>
# Examples:
status.HTTP_200_OK
status.HTTP_201_CREATED
status.HTTP_204_NO_CONTENT
status.HTTP_400_BAD_REQUEST
status.HTTP_401_UNAUTHORIZED
status.HTTP_403_FORBIDDEN
status.HTTP_404_NOT_FOUND
status.HTTP_500_INTERNAL_SERVER_ERROR
```

### CRUD Operations → Status Code Mapping

| Operation | Method | Success Code | Failure Codes |
|-----------|--------|-------------|---------------|
| List all | GET | 200 | 401, 403 |
| Get single | GET | 200 | 401, 403, 404 |
| Create | POST | 201 | 400, 401, 403, 409 |
| Update (full) | PUT | 200 | 400, 401, 403, 404 |
| Update (partial) | PATCH | 200 | 400, 401, 403, 404 |
| Delete | DELETE | 204 | 401, 403, 404 |

### Complete DRF View Example Using Proper Status Codes

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework.permissions import IsAuthenticated
from django.shortcuts import get_object_or_404
from .models import Product
from .serializers import ProductSerializer


class ProductListView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        # 200 OK
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = ProductSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)  # 201 Created
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)  # 400 Bad Request


class ProductDetailView(APIView):
    permission_classes = [IsAuthenticated]

    def get_object(self, pk):
        return get_object_or_404(Product, pk=pk)  # 404 if not found

    def get(self, request, pk):
        product = self.get_object(pk)
        serializer = ProductSerializer(product)
        return Response(serializer.data, status=status.HTTP_200_OK)  # 200 OK

    def put(self, request, pk):
        product = self.get_object(pk)
        serializer = ProductSerializer(product, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)  # 200 OK
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)  # 400

    def patch(self, request, pk):
        product = self.get_object(pk)
        serializer = ProductSerializer(product, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)  # 200 OK
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)  # 400

    def delete(self, request, pk):
        product = self.get_object(pk)
        product.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)  # 204 No Content
```

---

*End of HTTP Status Codes Complete Reference*
