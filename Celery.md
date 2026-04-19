# Celery — Complete Notes (Idiot-Proof Guide)

**With Django REST Framework (DRF) Integration**

---

- **Last Updated:** 2025
- **Level:** Beginner to Intermediate
- **Stack Covered:** Python, Celery, Redis, Django, Django REST Framework

---

## Table of Contents

1. [What is Celery? (The Big Picture)](#section-1)
2. [The 4 Core Components of Celery](#section-2)
3. [Celery Beat — The Task Scheduler](#section-3)
4. [Flower — Celery Monitoring Dashboard](#section-4)
5. [Celery + Django REST Framework (DRF) Integration](#section-5)
6. [Advantages and Disadvantages of Celery](#section-6)
7. [Tips, Tricks, and Best Practices](#section-7)
8. [Production Setup Checklist](#section-8)
9. [Common Errors and Fixes](#section-9)
10. [Quick Reference Cheatsheet](#section-10)

---

## Section 1 — What is Celery? (The Big Picture)

### Simple Definition

Imagine you run a restaurant. When a customer orders food, you don't make them stand at the counter and wait 30 minutes. You give them a token, tell them to sit down, and serve them when the food is ready. **That token system is Celery.**

In technical terms:

> **Celery** is a distributed task queue for Python. It lets you run heavy or time-consuming tasks (sending emails, resizing images, calling external APIs) in the **BACKGROUND** — so your main app stays fast and responsive.

| Without Celery | With Celery |
|---|---|
| User clicks "Send Email" → Server freezes for 5 seconds → User waits | User clicks "Send Email" → Server says "noted!" immediately → Email is sent in background → User is happy |

---

## Section 2 — The 4 Core Components of Celery

> Think of Celery like a factory assembly line with 4 roles.

---

### Component 1 — Producer

#### What It Is

The **Producer** is YOUR Django/Python application code. It is the part that **CREATES and SENDS** tasks to be done. It does NOT do the actual work — it just says "hey, someone do this!"

#### Real-World Analogy

A customer walking into a restaurant and placing an order. The customer (Producer) tells the waiter what they want. The customer doesn't cook the food themselves.

#### Example in Code

```python
from myapp.tasks import send_welcome_email

send_welcome_email.delay(user_id=42)
```

- `.delay()` is the magic word that says: **"run this in the background"**
- `.apply_async()` is the advanced version with more options

#### How It Works

1. Your Django view handles a request (e.g., user registers)
2. Instead of doing the slow work right there, it calls `task.delay()`
3. This packages the task and sends it to the Broker
4. Your view returns a response immediately — fast!

#### Tips & Tricks

- Always use `.delay()` for simple calls
- Use `.apply_async()` when you need countdown, eta, or priority
- Example with delay: `send_email.delay(user_id=5)`
- Example with eta: `send_email.apply_async(args=[5], countdown=60)` *(runs after 60 seconds)*

#### Best Practices

- Never put heavy logic inside a Django view directly
- Always offload emails, PDF generation, reports, scraping to tasks
- Keep task calls simple — just pass IDs, not full objects

---

### Component 2 — Broker

#### What It Is

The **Broker** is a **MESSAGE QUEUE** — a middleman that holds tasks between the Producer and the Worker. It receives tasks from the Producer and holds them until a Worker picks them up.

#### Real-World Analogy

A waiter at a restaurant. The customer (Producer) gives the order to the waiter. The waiter writes it down and passes it to the kitchen (Worker). The waiter doesn't cook — they just carry and manage orders.

#### Popular Brokers

| Broker | Notes |
|---|---|
| **Redis** | Most popular, fast, easy to set up *(RECOMMENDED for beginners)* |
| **RabbitMQ** | More powerful, great for complex routing, slightly harder to set up |
| **Amazon SQS** | For AWS cloud-based deployments |

#### Setup with Redis (Most Common)

Install Redis on your machine or use Docker:

```bash
docker run -d -p 6379:6379 redis
```

In `settings.py`:

```python
CELERY_BROKER_URL = 'redis://localhost:6379/0'
```

#### How It Works

1. Producer sends task → Broker stores it in a queue
2. Broker holds tasks safely even if Workers are busy
3. Workers pick up tasks one by one from the Broker
4. If Worker crashes, Broker keeps the task *(depending on config)*

#### Tips & Tricks

- Use **Redis** for development and small-to-medium production apps
- Use **RabbitMQ** when you need multiple queues, priorities, dead-letter queues
- Always make sure your Broker is running **BEFORE** starting Celery workers
- Monitor Redis memory — tasks can pile up if workers are too slow

#### Best Practices

- Use separate Redis databases for Broker and Cache (e.g., `/0` for broker, `/1` for cache)
- In production, use Redis with persistence enabled (AOF or RDB)
- Set `CELERY_BROKER_CONNECTION_RETRY_ON_STARTUP = True` in newer Celery versions

---

### Component 3 — Worker

#### What It Is

The **Worker** is a **SEPARATE PROCESS** that runs in the background, listens to the Broker, picks up tasks, and **EXECUTES** them. Workers are the actual cooks in the kitchen.

#### Real-World Analogy

The kitchen staff in a restaurant. They take orders from the waiter (Broker), cook the food, and send it out. They work independently — multiple chefs can cook at the same time.

#### How to Start a Worker

In your terminal (inside your Django project folder):

```bash
celery -A your_project_name worker --loglevel=info
```

Example if your project is called `mysite`:

```bash
celery -A mysite worker --loglevel=info
```

To run multiple worker processes (concurrency):

```bash
celery -A mysite worker --loglevel=info --concurrency=4
```

#### How It Works

1. Worker starts and connects to the Broker
2. Worker listens for new tasks
3. When a task arrives, Worker executes the Python function
4. Worker reports success/failure to the Result Backend *(if configured)*
5. Worker picks up the next task

#### Concurrency Options

| Pool | Best For |
|---|---|
| `--pool=gevent` | I/O-heavy tasks (API calls, email sending) |
| `--pool=solo` | Debugging (single process, easier to trace errors) |
| `--pool=threads` | Thread-based concurrency |

#### Tips & Tricks

- Use `--loglevel=debug` during development to see everything
- Use `--loglevel=info` in production
- Run workers with a process manager like **Supervisor** or **systemd** in production
- Use separate queues for different task priorities *(explained in Best Practices)*

#### Best Practices

- Always run workers in a **separate terminal / process** from Django
- In production: use Supervisor, systemd, or Docker to manage workers
- Set task time limits to prevent runaway tasks:

```python
@app.task(time_limit=300)       # hard kill after 5 minutes
@app.task(soft_time_limit=240)  # raises exception after 4 minutes
```

- Use task routing to send heavy tasks to dedicated workers

---

### Component 4 — Result Backend

#### What It Is

The **Result Backend** is where Celery **STORES THE RESULT** of a task after it finishes. It is **optional** — you only need it if you want to check whether a task succeeded, failed, or get its return value.

#### Real-World Analogy

The receipt/notification system at a restaurant. After the food is cooked and served, a record is kept. You can check your receipt to confirm your order was completed.

#### Popular Result Backends

| Backend | Notes |
|---|---|
| **Redis** | Most common (same Redis used as Broker is fine) |
| **Django ORM** | Stores results in your Django database (`django-celery-results`) |
| **Database** | Any SQL database via SQLAlchemy |

#### Setup with Redis

```python
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
```

#### Setup with Django Database (`django-celery-results`)

```bash
pip install django-celery-results
```

```python
# settings.py
INSTALLED_APPS = [..., 'django_celery_results']
CELERY_RESULT_BACKEND = 'django-db'
```

```bash
python manage.py migrate
```

#### How to Check Task Status

```python
result = send_email.delay(user_id=5)

print(result.id)      # Task ID (UUID)
print(result.status)  # PENDING, STARTED, SUCCESS, FAILURE, RETRY
print(result.result)  # The return value (if SUCCESS)
result.get(timeout=10)  # Wait for result (blocking)
```

#### Tips & Tricks

- **Don't** use `result.get()` in a Django view — it blocks the request!
- Store task IDs in your database if users need to check task status
- Set `CELERY_RESULT_EXPIRES` to auto-clean old results:

```python
CELERY_RESULT_EXPIRES = 3600  # 1 hour
```

#### Best Practices

- Only enable Result Backend if you actually need to track task results
- Without a Result Backend, Celery is "fire and forget" *(often that's fine!)*
- Use `django-celery-results` if you want results visible in Django Admin

---

## Section 3 — Celery Beat — The Task Scheduler

### What is Celery Beat?

**Celery Beat** is Celery's **BUILT-IN SCHEDULER**. It lets you run tasks automatically at specific times or intervals — like a cron job, but managed through Python/Django.

#### Real-World Analogy

An alarm clock that automatically triggers actions. Example: Every morning at 8 AM, send a summary email. You set it once, and it fires forever on schedule.

### Use Cases

- Send daily/weekly digest emails
- Clean up expired tokens every hour
- Generate reports every midnight
- Sync data from an external API every 30 minutes
- Check inventory levels every 15 minutes

### Installation

```bash
pip install django-celery-beat
```

```python
# settings.py
INSTALLED_APPS = [..., 'django_celery_beat']
CELERY_BEAT_SCHEDULER = 'django_celery_beat.schedulers:DatabaseScheduler'
```

```bash
python manage.py migrate
```

### How to Start Celery Beat

In a **SEPARATE** terminal (Beat and Worker are different processes):

```bash
celery -A mysite beat --loglevel=info
```

Or start both worker and beat together *(development only)*:

```bash
celery -A mysite worker --beat --loglevel=info
```

### Defining Scheduled Tasks — Method 1 (`settings.py`)

```python
from celery.schedules import crontab

CELERY_BEAT_SCHEDULE = {
    'send-daily-report': {
        'task': 'myapp.tasks.send_daily_report',
        'schedule': crontab(hour=8, minute=0),  # Every day at 8:00 AM
    },
    'clean-expired-tokens': {
        'task': 'myapp.tasks.clean_tokens',
        'schedule': 3600.0,  # Every 3600 seconds (1 hour)
    },
}
```

### Crontab Examples

```python
crontab()                                           # every minute
crontab(minute=0, hour=0)                           # midnight every day
crontab(minute=30, hour='*/2')                      # every 2 hours at :30
crontab(day_of_week='monday')                       # every Monday at midnight
crontab(day_of_month='1', month_of_year='1')        # January 1st only
```

### Defining Scheduled Tasks — Method 2 (Django Admin via `django-celery-beat`)

- Install `django-celery-beat`
- Go to **Django Admin → Periodic Tasks**
- Add tasks through the UI — no code changes needed!
- Great for non-developer team members to manage schedules

### Tips & Tricks

- Always run Beat as a **SEPARATE** process from the Worker
- Only run **ONE** Beat process — multiple Beat processes will duplicate task execution
- Use `django-celery-beat` for dynamic scheduling (change schedules without restarting)
- For simple static schedules, `settings.py` `CELERY_BEAT_SCHEDULE` is fine

### Best Practices

- Use `CELERY_TIMEZONE` to set your project timezone:

```python
CELERY_TIMEZONE = 'Asia/Kolkata'
USE_TZ = True
```

- Always name your scheduled tasks clearly (e.g., `'send-weekly-digest-email'`)
- Test scheduled tasks manually first using `.delay()` before scheduling
- Monitor Beat logs to confirm tasks are being dispatched on time

### Advantages of Celery Beat

- ✅ Managed in Python/Django — no need to configure OS cron jobs
- ✅ Can be updated dynamically via Django Admin (with `django-celery-beat`)
- ✅ Integrates perfectly with the rest of Celery
- ✅ Timezone-aware scheduling

### Disadvantages of Celery Beat

- ❌ Must run as a separate process
- ❌ Only **ONE** Beat process should run at a time (coordination needed in multi-server setups)
- ❌ If Beat crashes, scheduled tasks won't fire until it's restarted
- ❌ In multi-server production, needs a distributed lock to avoid duplicate scheduling

---

## Section 4 — Flower — Celery Monitoring Dashboard

### What is Flower?

**Flower** is a **WEB-BASED REAL-TIME MONITORING TOOL** for Celery. It gives you a dashboard where you can see:

- Which tasks are running, succeeded, or failed
- Worker status (online/offline, how busy they are)
- Task history and timings
- Control workers (restart, stop, rate-limit) from the browser

#### Real-World Analogy

The manager's office with CCTV screens showing every kitchen station. You can see what every chef (worker) is doing in real time, check past orders (task history), and intervene if something goes wrong.

### Installation

```bash
pip install flower
```

### How to Start Flower

```bash
celery -A mysite flower
```

Then open in browser: **http://localhost:5555**

```bash
# Custom port
celery -A mysite flower --port=5566

# With basic auth (recommended in production)
celery -A mysite flower --basic_auth=admin:yourpassword
```

### What You Can See in Flower

#### Dashboard Tab
- Number of workers online/offline
- Tasks processed (total, per minute)
- Tasks failed

#### Workers Tab
- Each worker process listed
- Tasks currently being processed
- Worker uptime, concurrency, prefetch count

#### Tasks Tab
- All recent tasks with UUID, state, runtime, arguments
- Filter by state: `SUCCESS`, `FAILURE`, `PENDING`, `STARTED`, `RETRY`
- Click any task to see full details (args, traceback if failed)

#### Broker Tab
- Number of messages waiting in each queue
- Useful for spotting queue backlogs

### Tips & Tricks

- Always run Flower **SEPARATELY** from your Django server and Workers
- In production, put Flower behind a reverse proxy (Nginx) with auth
- Flower is mostly READ-ONLY, but you **CAN** revoke tasks or restart workers
- Use `--max_tasks=5000` to limit how many tasks are kept in memory

### Best Practices

- **Never** expose Flower to the public internet without authentication
- In production: use basic auth or integrate with your auth system
- Use Flower as a debugging tool when tasks are failing or piling up
- Set up email/Slack alerts via Celery signals instead of relying only on Flower

### Advantages of Flower

- ✅ Beautiful, simple web UI — no setup beyond `pip install`
- ✅ Real-time updates (uses WebSockets)
- ✅ Can inspect task arguments and tracebacks
- ✅ Can revoke (cancel) pending tasks
- ✅ Completely free and open source

### Disadvantages of Flower

- ❌ No built-in alerting (won't email you when a task fails)
- ❌ Task history is lost when Flower restarts (unless using persistent storage)
- ❌ Not a replacement for a proper monitoring system (like Prometheus + Grafana)
- ❌ Requires separate process management in production

---

## Section 5 — Celery + Django REST Framework (DRF) Integration

### Full Project Setup — Step by Step

#### Step 1 — Install Packages

```bash
pip install celery redis django-celery-results django-celery-beat flower
```

#### Step 2 — Project Structure

```
mysite/
├── mysite/
│   ├── __init__.py   ← Import Celery app here
│   ├── celery.py     ← Celery configuration
│   ├── settings.py   ← Django + Celery settings
│   └── urls.py
├── myapp/
│   ├── tasks.py      ← All your Celery tasks
│   ├── views.py      ← DRF views that trigger tasks
│   └── models.py
└── manage.py
```

#### Step 3 — Create `mysite/celery.py`

```python
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.settings')

app = Celery('mysite')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()
```

#### Step 4 — Update `mysite/__init__.py`

```python
from .celery import app as celery_app

__all__ = ('celery_app',)
```

#### Step 5 — Update `settings.py`

```python
INSTALLED_APPS = [
    ...
    'django_celery_results',
    'django_celery_beat',
]

# Broker (Redis)
CELERY_BROKER_URL = 'redis://localhost:6379/0'

# Result Backend
CELERY_RESULT_BACKEND = 'django-db'  # or 'redis://localhost:6379/0'

# Serialization
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'

# Timezone
CELERY_TIMEZONE = 'Asia/Kolkata'
USE_TZ = True

# Beat Scheduler (if using django-celery-beat)
CELERY_BEAT_SCHEDULER = 'django_celery_beat.schedulers:DatabaseScheduler'
```

#### Step 6 — Create `myapp/tasks.py`

```python
from celery import shared_task
from django.core.mail import send_mail

@shared_task
def send_welcome_email(user_id):
    from django.contrib.auth.models import User
    user = User.objects.get(id=user_id)
    send_mail(
        subject='Welcome!',
        message=f'Hello {user.username}, welcome to our platform!',
        from_email='noreply@example.com',
        recipient_list=[user.email],
    )
    return f"Email sent to {user.email}"


@shared_task(bind=True, max_retries=3)
def process_payment(self, order_id):
    try:
        # payment processing logic here
        pass
    except Exception as exc:
        raise self.retry(exc=exc, countdown=60)  # retry after 60s
```

#### Step 7 — Use in a DRF View (`views.py`)

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .tasks import send_welcome_email, process_payment

class UserRegistrationView(APIView):
    def post(self, request):
        # ... create user logic ...
        user = create_user(request.data)

        # Trigger background task — does NOT block the response
        send_welcome_email.delay(user.id)

        return Response(
            {'message': 'User created. Welcome email will be sent shortly.'},
            status=status.HTTP_201_CREATED
        )

class OrderView(APIView):
    def post(self, request):
        order = create_order(request.data)

        # Run payment processing in background
        task = process_payment.delay(order.id)

        return Response(
            {'order_id': order.id, 'task_id': task.id},
            status=status.HTTP_202_ACCEPTED
        )
```

#### Step 8 — Check Task Status via API (Optional)

```python
from celery.result import AsyncResult

class TaskStatusView(APIView):
    def get(self, request, task_id):
        result = AsyncResult(task_id)
        data = {
            'task_id': task_id,
            'status': result.status,   # PENDING, SUCCESS, FAILURE
            'result': result.result if result.successful() else None,
            'error': str(result.result) if result.failed() else None,
        }
        return Response(data)

# Add to urls.py:
# path('tasks/<str:task_id>/', TaskStatusView.as_view())
```

### Common DRF + Celery Patterns

#### Pattern 1 — Fire and Forget *(Most Common)*

User triggers action → task runs in background → API returns immediately.

**Example use cases:** send email, send SMS, log analytics event

```python
task.delay(args)  # → Response immediately
```

#### Pattern 2 — Polling for Status

User triggers long task → API returns `task_id` → Client polls status endpoint.

**Example use cases:** PDF generation, video processing, bulk export

```python
task = heavy_task.delay(args)
return Response({'task_id': task.id})
# Client polls GET /tasks/{task_id}/ every 2 seconds
```

#### Pattern 3 — Webhook Callback

Task completes → task itself sends result to a webhook URL.

**Good for:** third-party integrations, async notifications

#### Pattern 4 — Chaining Tasks (Celery Canvas)

```python
from celery import chain

workflow = chain(validate_data.s(data_id), process_data.s(), send_report.s())
workflow.delay()
# Tasks run one after another, output of each feeds into the next
```

#### Pattern 5 — Parallel Tasks (Celery Canvas — Group)

```python
from celery import group

jobs = group(process_item.s(item_id) for item_id in item_ids)
result = jobs.delay()
# All tasks run in parallel
```

---

## Section 6 — Advantages and Disadvantages of Celery

### Advantages

| # | Advantage | Description |
|---|---|---|
| 1 | **Fast Responses** | Your API never makes users wait for slow operations |
| 2 | **Scalable** | Add more workers as load increases |
| 3 | **Reliable** | Tasks survive worker crashes (with proper config) |
| 4 | **Flexible** | Works with many brokers, backends, and frameworks |
| 5 | **Retry Support** | Automatic retries with backoff on failure |
| 6 | **Scheduling** | Built-in cron-like scheduling via Celery Beat |
| 7 | **Monitoring** | Flower gives real-time visibility |
| 8 | **Django Integration** | Seamless, well-documented Django integration |
| 9 | **Canvas API** | Chain, group, chord — powerful task orchestration |
| 10 | **Open Source** | Free, widely used, massive community |

### Disadvantages

| # | Disadvantage | Description |
|---|---|---|
| 1 | **Complexity** | More moving parts (broker, worker, beat, backend) |
| 2 | **Hard to Debug** | Tricky to debug background tasks vs sync code |
| 3 | **Setup Overhead** | Requires Redis/RabbitMQ running separately |
| 4 | **Versioning Issues** | Celery has had many breaking changes between versions |
| 5 | **Resource Usage** | Workers consume memory/CPU even when idle |
| 6 | **No Ordering Guarantee** | Tasks may not run in the order you expect |
| 7 | **Serialization** | Objects passed to tasks must be JSON-serializable |
| 8 | **Testing** | Unit testing Celery tasks requires special setup |

---

## Section 7 — Tips, Tricks, and Best Practices

### Tips & Tricks

#### Tip 1 — Pass Only IDs to Tasks, Not Objects

```python
# BAD
send_email.delay(user_object)   # may not serialize correctly

# GOOD
send_email.delay(user_id=42)    # just pass the DB id
```

#### Tip 2 — Use `shared_task` Instead of `app.task`

```python
@shared_task
def my_task():
    ...
```

- Use `@shared_task` in your app's `tasks.py`
- Avoids tight coupling to the Celery app instance
- Works better in reusable Django apps

#### Tip 3 — Always Set Task Retries

```python
@shared_task(bind=True, max_retries=3, default_retry_delay=60)
def my_task(self):
    try:
        ...
    except SomeException as exc:
        raise self.retry(exc=exc)
```

#### Tip 4 — Use Task Names Explicitly

```python
@shared_task(name='myapp.tasks.send_email')
def send_email(user_id):
    ...
```

Explicit names prevent issues if you rename/move task files.

#### Tip 5 — Separate Queues for Priority

```python
CELERY_TASK_ROUTES = {
    'myapp.tasks.send_email':       {'queue': 'email'},
    'myapp.tasks.process_payment':  {'queue': 'payments'},
    'myapp.tasks.generate_report':  {'queue': 'reports'},
}
```

Start dedicated workers per queue:

```bash
celery -A mysite worker -Q email --concurrency=4
celery -A mysite worker -Q payments --concurrency=2
```

#### Tip 6 — Always Set Time Limits

```python
@shared_task(time_limit=300, soft_time_limit=240)
def long_running_task():
    ...
```

Prevents zombie tasks that run forever.

#### Tip 7 — Testing Celery Tasks

In tests, use `CELERY_TASK_ALWAYS_EAGER = True` in test settings. This makes tasks run synchronously during tests (no worker needed).

Or mock the task call:

```python
from unittest.mock import patch

with patch('myapp.tasks.send_email.delay') as mock_task:
    response = self.client.post('/register/', data)
    mock_task.assert_called_once_with(user_id=expected_id)
```

#### Tip 8 — Idempotency — Make Tasks Safe to Retry

Design tasks so running them twice gives the same result.

```python
# BAD
INSERT INTO table...           # creates duplicates on retry

# GOOD
INSERT OR IGNORE / update_or_create   # safe to retry
```

#### Tip 9 — Avoid Long Chains in Production

Deep task chains can cause issues with result backend memory. Break large workflows into smaller independent pieces.

#### Tip 10 — Log Inside Tasks

```python
import logging
logger = logging.getLogger(__name__)

@shared_task
def my_task():
    logger.info("Task started")
    # ... do work ...
    logger.info("Task completed")
```

### Best Practices Summary

1. Always run Redis/RabbitMQ as a managed service in production (e.g., Redis Cloud, AWS ElastiCache)
2. Use **Supervisor** or **systemd** to manage Celery worker processes in production
3. Enable task acknowledgement late: `CELERY_TASK_ACKS_LATE = True` (safer for crashes)
4. Set `CELERY_WORKER_PREFETCH_MULTIPLIER = 1` to prevent tasks from being "hoarded" by one worker
5. Monitor your queue lengths — a growing queue means workers can't keep up
6. Use **Flower** in development; use **Prometheus + Grafana** for production monitoring
7. Set task expiry: `@shared_task(expires=3600)` to discard stale tasks
8. Keep tasks **small and focused** — one task, one responsibility
9. **Never** access `request.session` or `request.user` inside a Celery task (no HTTP context!)
10. Document every task: what it does, what args it takes, what it returns

---

## Section 8 — Production Setup Checklist

### Infrastructure

- [ ] Redis running and secured (password protected)
- [ ] Workers managed by Supervisor or systemd
- [ ] Beat running as single separate process
- [ ] Flower running behind Nginx with auth

### `settings.py`

- [ ] `CELERY_BROKER_URL` set to production Redis
- [ ] `CELERY_RESULT_BACKEND` configured
- [ ] `CELERY_TIMEZONE` set correctly
- [ ] `CELERY_TASK_ACKS_LATE = True`
- [ ] `CELERY_WORKER_PREFETCH_MULTIPLIER = 1`
- [ ] `CELERY_RESULT_EXPIRES = 86400` (24 hours)
- [ ] `CELERY_TASK_SERIALIZER = 'json'`

### Tasks

- [ ] All tasks use `@shared_task`
- [ ] All tasks pass IDs, not objects
- [ ] All tasks have retries configured
- [ ] All tasks have time limits set
- [ ] All tasks are idempotent

### Monitoring

- [ ] Flower or equivalent monitoring set up
- [ ] Alerts configured for task failures
- [ ] Queue length monitoring in place

---

## Section 9 — Common Errors and Fixes

| Error | Fix |
|---|---|
| `"Task received but no worker is running"` | Start your Celery worker: `celery -A mysite worker --loglevel=info` |
| `"Connection refused" / "Error connecting to Redis"` | Make sure Redis is running: `redis-cli ping` (should return `PONG`) |
| `"Task not found / Not registered"` | Make sure `tasks.py` is in an installed app and `app.autodiscover_tasks()` is set |
| `"Cannot serialize object"` | Only pass JSON-serializable args (strings, numbers, lists, dicts) to tasks |
| `"kombu.exceptions.OperationalError"` | Broker (Redis) is down or unreachable — check connection settings |
| `"Soft time limit exceeded"` | Your task is taking too long — optimize the task or increase the time limit |
| `Beat is running but tasks are not firing` | Check that Beat is connected to the same broker as Workers. Check `CELERY_TIMEZONE` matches your expected schedule timezone. Check `CELERY_BEAT_SCHEDULE` entries for typos in task names. |

---

## Section 10 — Quick Reference Cheatsheet

### Commands

```bash
# Start worker
celery -A mysite worker --loglevel=info

# Start beat
celery -A mysite beat --loglevel=info

# Start flower
celery -A mysite flower

# List active tasks
celery -A mysite inspect active

# Purge all tasks
celery -A mysite purge
```

### Task States

| State | Meaning |
|---|---|
| `PENDING` | Task is waiting to be picked up |
| `STARTED` | Worker has picked it up and is running it |
| `SUCCESS` | Completed successfully |
| `FAILURE` | An exception was raised |
| `RETRY` | Task failed and is scheduled to retry |
| `REVOKED` | Task was manually cancelled |

### Task Calling

```python
task.delay(arg1, arg2)
task.apply_async(args=[arg1, arg2], countdown=60)
task.apply_async(args=[arg1], eta=datetime(2025, 1, 1, 8, 0))
```

### Crontab Shortcuts

```python
crontab()                                          # Every minute
crontab(minute=0)                                  # Every hour
crontab(hour=8, minute=0)                          # Every day at 8 AM
crontab(hour=9, minute=0, day_of_week='monday')    # Every Monday 9 AM
crontab(hour=0, minute=0, day_of_month='1')        # First of every month
```

---

*End of Notes — Celery Complete Guide*


