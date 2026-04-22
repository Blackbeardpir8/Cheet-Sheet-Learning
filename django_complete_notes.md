# Django Complete Notes

> **Beginner-friendly, step-by-step Django notes covering everything from Models to Security.**

---

## Table of Contents

1. [Models (Database Foundation)](#1-models-database-foundation)
   - [Built-in Field Types](#built-in-field-types)
   - [Built-in Field Options](#built-in-field-options)
   - [Model Operations](#model-operations)
   - [Retrieving Data from Database](#retrieving-data-from-database)

2. [Model Relationships](#2-model-relationships)
   - [One to One Relationship](#one-to-one-relationship)
   - [Many to One Relationship](#many-to-one-relationship)
   - [Many to Many Relationship](#many-to-many-relationship)
   - [on_delete](#on_delete)

3. [QuerySet API](#3-queryset-api)
   - [Methods that return new QuerySets](#methods-that-return-new-querysets)
   - [Methods that do not return new QuerySets](#methods-that-do-not-return-new-querysets)
   - [Operators that return new QuerySets](#operators-that-return-new-querysets)
   - [Field Lookups](#field-lookups)
   - [Q Object](#q-object)
   - [Aggregation](#aggregation)
   - [Aggregation Functions](#aggregation-functions)
   - [Limiting QuerySets](#limiting-querysets)

4. [Custom User & Authentication](#4-custom-user--authentication)
   - [Default User Model](#default-user-model)
   - [Custom User Model using AbstractUser](#custom-user-model-using-abstractuser)
   - [Custom User Model using AbstractBaseUser](#custom-user-model-using-abstractbaseuser)
   - [Custom User Manager](#custom-user-manager)
   - [AUTH_USER_MODEL Setting](#auth_user_model-setting)
   - [Authentication Functions](#authentication-functions)
   - [Permissions and Groups](#permissions-and-groups)

5. [Built-in Authentication Tools](#5-built-in-authentication-tools)
   - [Built-in Authentication](#built-in-authentication)
   - [login_required Decorator](#login_required-decorator)
   - [staff_member_required Decorator](#staff_member_required-decorator)
   - [permission_required Decorator](#permission_required-decorator)
   - [Authentication Views](#authentication-views)
   - [Built-in Forms](#built-in-forms)
   - [Auth Settings](#auth-settings)

6. [Register Model (Admin)](#6-register-model-admin)
   - [admin.py File](#adminpy-file)
   - [Register Model Without Model Admin](#register-model-without-model-admin)
   - [str Method](#str-method)
   - [Model Admin](#model-admin)
   - [Model Admin without decorator](#model-admin-without-decorator)
   - [Model Admin with decorator](#model-admin-with-decorator)
   - [Commonly used Model Admin attributes](#commonly-used-model-admin-attributes)

7. [Django Forms Basics](#7-django-forms-basics)
   - [Creating Django Form](#creating-django-form)
   - [Form API](#form-api)
   - [Bound and Unbound Forms](#bound-and-unbound-forms)
   - [Creating Form Object](#creating-form-object)
   - [Display form to User](#display-form-to-user)
   - [Using Form object in Template File](#using-form-object-in-template-file)

8. [Form Fields & Widgets](#8-form-fields--widgets)
   - [Form Field](#form-field)
   - [Built-in Form Fields](#built-in-form-fields)
   - [Field Arguments](#field-arguments)
   - [Widget](#widget)
   - [Built-in Widgets](#built-in-widgets)

9. [Form Rendering](#9-form-rendering)
   - [Form Rendering Options](#form-rendering-options)
   - [Render Form Field Manually](#render-form-field-manually)

10. [Form Handling](#10-form-handling)
    - [GET and POST](#get-and-post)
    - [Cross Site Request Forgery](#cross-site-request-forgery)
    - [Django CSRF Protection](#django-csrf-protection)

11. [Form Validation](#11-form-validation)
    - [is_valid Method](#is_valid-method)
    - [Built-in Field Validation](#built-in-field-validation)
    - [Custom Validation](#custom-validation)
    - [Built-in Validators](#built-in-validators)
    - [Custom Validators](#custom-validators)

12. [Form Errors](#12-form-errors)
    - [Form Errors](#form-errors)
    - [Field Errors](#field-errors)
    - [Non Field Errors](#non-field-errors)
    - [Styling Django Form Errors](#styling-django-form-errors)

13. [Model Forms](#13-model-forms)
    - [Model Form](#model-form)
    - [Selecting Fields](#selecting-fields)
    - [Model Form Inheritance](#model-form-inheritance)

14. [Function vs Class Based Views](#14-function-vs-class-based-views)
    - [Function Based View](#function-based-view)
    - [Class Based View](#class-based-view)
    - [FBV vs CBV Comparison](#fbv-vs-cbv-comparison)

15. [Base Class-Based Views](#15-base-class-based-views)
    - [View](#view)
    - [TemplateView](#templateview)
    - [RedirectView](#redirectview)

16. [Generic Class-Based Views](#16-generic-class-based-views)
    - [ListView](#listview)
    - [DetailView](#detailview)
    - [FormView](#formview)
    - [CreateView](#createview)
    - [UpdateView](#updateview)
    - [DeleteView](#deleteview)

17. [Decorators in Views](#17-decorators-in-views)
    - [Method Decorator](#method-decorator)
    - [Decorating in URLconf](#decorating-in-urlconf)
    - [Decorating in the Class](#decorating-in-the-class)

18. [Utilities](#18-utilities)
    - [JSONResponse](#jsonresponse)
    - [reverse and reverse_lazy](#reverse-and-reverse_lazy)
    - [Namespace](#namespace)

19. [Query Optimization](#19-query-optimization)
    - [select_related and prefetch_related](#select_related-and-prefetch_related)
    - [Annotate](#annotate)

20. [Pagination](#20-pagination)

21. [Caching](#21-caching)
    - [How Cache works](#how-cache-works)
    - [How to Implement Caching](#how-to-implement-caching)
    - [Cache Argument Types](#cache-argument-types)
    - [Per-site cache](#per-site-cache)
    - [Per-view cache](#per-view-cache)
    - [Template Fragment Caching](#template-fragment-caching)
    - [Caching Backends](#caching-backends)

22. [Cookies](#22-cookies)
    - [Django Cookies](#django-cookies)
    - [Creating Cookies](#creating-cookies)
    - [Reading Cookies](#reading-cookies)
    - [Replace or Append Cookies](#replace-or-append-cookies)
    - [Deleting Cookies](#deleting-cookies)
    - [Signed Cookies](#signed-cookies)
    - [Security issues & Limitations](#security-issues--limitations)

23. [Sessions](#23-sessions)
    - [Session Types](#session-types)
    - [Using Session in views](#using-session-in-views)
    - [Session Methods](#session-methods)
    - [Session Settings](#session-settings)

24. [Middleware](#24-middleware)
    - [How Middleware Works](#how-middleware-works)
    - [Function based Middleware](#function-based-middleware)
    - [Class based Middleware](#class-based-middleware)
    - [Activating Middleware](#activating-middleware)
    - [Middleware Hooks](#middleware-hooks)
    - [TemplateResponse](#templateresponse)
    - [Built-in Middleware](#built-in-middleware)

25. [Signals](#25-signals)
    - [Built-in Signals](#built-in-signals)
    - [Custom Signals](#custom-signals)
    - [Defining Custom Signals](#defining-custom-signals)
    - [Sending Signals](#sending-signals)
    - [Disconnecting Signals](#disconnecting-signals)

26. [Django Security](#26-django-security)

27. [Logging](#27-logging)

28. [Static Files](#28-static-files)
    - [Creating Static Files](#creating-static-files)
    - [Using Static Files](#using-static-files)
    - [Complete Example](#complete-example)

29. [Template Tags for Static](#29-template-tags-for-static)

30. [Settings for Static Files](#30-settings-for-static-files)

---

## 1. Models (Database Foundation)

> **Definition:** A Model is a Python class that represents a database table. Each attribute of the class is a column in the table.

**Why use it?** Instead of writing raw SQL, Django models let you interact with the database using Python code.

---

### Built-in Field Types

| Field | Description | Example |
|---|---|---|
| `CharField` | Short text | `name = CharField(max_length=100)` |
| `TextField` | Long text | `bio = TextField()` |
| `IntegerField` | Integer number | `age = IntegerField()` |
| `FloatField` | Decimal (float) | `price = FloatField()` |
| `DecimalField` | Precise decimal | `salary = DecimalField(max_digits=10, decimal_places=2)` |
| `BooleanField` | True/False | `is_active = BooleanField()` |
| `DateField` | Date only | `dob = DateField()` |
| `DateTimeField` | Date + Time | `created_at = DateTimeField()` |
| `TimeField` | Time only | `start_time = TimeField()` |
| `EmailField` | Email address | `email = EmailField()` |
| `URLField` | URL | `website = URLField()` |
| `SlugField` | URL-friendly text | `slug = SlugField()` |
| `ImageField` | Image upload | `photo = ImageField(upload_to='photos/')` |
| `FileField` | File upload | `resume = FileField(upload_to='resumes/')` |
| `UUIDField` | UUID | `uid = UUIDField()` |
| `JSONField` | JSON data | `data = JSONField()` |
| `AutoField` | Auto-increment PK | set automatically |
| `BigAutoField` | Big int PK | set automatically |

---

### Built-in Field Options

These options can be passed to **any** field:

```python
class Product(models.Model):
    name = models.CharField(
        max_length=100,       # max length for CharField
        null=True,            # allow NULL in DB
        blank=True,           # allow empty in forms
        default='Unknown',    # default value
        unique=True,          # must be unique
        db_index=True,        # create a DB index
        verbose_name='Product Name',  # human-readable name
        help_text='Enter product name',
        editable=False,       # not shown in forms/admin
        choices=[('S','Small'), ('M','Medium'), ('L','Large')],
        primary_key=False,    # set as PK
    )
```

- `null=True` → DB stores NULL
- `blank=True` → Form allows empty input
- `default` → Default value if none provided
- `unique=True` → No duplicates allowed
- `choices` → Dropdown in admin/forms

---

### Model Operations

```python
# models.py
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    email = models.EmailField(unique=True)

    class Meta:
        ordering = ['name']         # default ordering
        verbose_name = 'Student'
        verbose_name_plural = 'Students'
        db_table = 'student_table'  # custom table name

    def __str__(self):
        return self.name
```

**Run migrations:**
```bash
python manage.py makemigrations
python manage.py migrate
```

**CRUD Operations:**
```python
# CREATE
s = Student.objects.create(name='Ali', age=20, email='ali@mail.com')

# READ
all_students = Student.objects.all()
one = Student.objects.get(id=1)

# UPDATE
s = Student.objects.get(id=1)
s.name = 'Ahmed'
s.save()

# DELETE
s = Student.objects.get(id=1)
s.delete()
```

---

### Retrieving Data from Database

```python
# All records
Student.objects.all()

# Filter (returns QuerySet)
Student.objects.filter(age=20)

# Exclude
Student.objects.exclude(age=20)

# Get single object (raises error if not found or multiple)
Student.objects.get(id=1)

# Order by
Student.objects.order_by('name')        # A-Z
Student.objects.order_by('-name')       # Z-A

# First and Last
Student.objects.first()
Student.objects.last()

# Count
Student.objects.count()

# Check existence
Student.objects.filter(age=20).exists()

# Values (returns dict)
Student.objects.values('name', 'age')

# Values list (returns tuples)
Student.objects.values_list('name', flat=True)
```

---

## 2. Model Relationships

> **Definition:** Relationships connect two models (tables) together in the database.

---

### One to One Relationship

- **One** record in table A maps to **one** record in table B.
- Use `OneToOneField`.

```python
class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()

# Access
profile = user.userprofile
user = profile.user
```

---

### Many to One Relationship

- **Many** records in table A map to **one** record in table B.
- Use `ForeignKey`.

```python
class Post(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    title = models.CharField(max_length=200)

# Access
posts = user.posts.all()      # All posts by a user
author = post.author          # Author of a post
```

---

### Many to Many Relationship

- **Many** records in A map to **many** records in B.
- Use `ManyToManyField`.

```python
class Student(models.Model):
    name = models.CharField(max_length=100)
    courses = models.ManyToManyField('Course', related_name='students')

class Course(models.Model):
    title = models.CharField(max_length=100)

# Access
student.courses.all()         # All courses for a student
course.students.all()         # All students in a course

# Add / Remove
student.courses.add(course)
student.courses.remove(course)
student.courses.clear()
```

---

### on_delete

When a referenced record is deleted, `on_delete` decides what happens:

| Option | Behavior |
|---|---|
| `CASCADE` | Delete related records too |
| `PROTECT` | Prevent deletion if related records exist |
| `SET_NULL` | Set FK to NULL (requires `null=True`) |
| `SET_DEFAULT` | Set FK to default value |
| `DO_NOTHING` | Do nothing (can cause DB errors) |
| `RESTRICT` | Prevent deletion (raises RestrictedError) |

```python
author = models.ForeignKey(User, on_delete=models.CASCADE)
author = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
```

---

## 3. QuerySet API

> **Definition:** A QuerySet is a list of objects from the database. It is lazy — it only hits the DB when evaluated.

---

### Methods that return new QuerySets

```python
# filter() - filter records
Student.objects.filter(age__gt=18)

# exclude() - exclude records
Student.objects.exclude(age=20)

# order_by() - sort
Student.objects.order_by('-name')

# distinct() - remove duplicates
Student.objects.values('city').distinct()

# select_related() - join FK in single query
Post.objects.select_related('author')

# prefetch_related() - prefetch M2M/reverse FK
Student.objects.prefetch_related('courses')

# annotate() - add computed field
from django.db.models import Count
Student.objects.annotate(num_courses=Count('courses'))

# values() - return dict
Student.objects.values('name', 'age')

# values_list() - return tuples
Student.objects.values_list('name', flat=True)

# only() - fetch only specified fields
Student.objects.only('name')

# defer() - fetch all except specified fields
Student.objects.defer('bio')

# none() - empty queryset
Student.objects.none()

# union() - combine querysets
qs1.union(qs2)

# reverse() - reverse ordering
Student.objects.order_by('name').reverse()
```

---

### Methods that do not return new QuerySets

```python
# get() - single object
Student.objects.get(id=1)

# create() - create and save
Student.objects.create(name='Ali', age=20)

# get_or_create() - get or create
obj, created = Student.objects.get_or_create(name='Ali', defaults={'age': 20})

# update_or_create()
obj, created = Student.objects.update_or_create(name='Ali', defaults={'age': 21})

# count()
Student.objects.count()

# exists()
Student.objects.filter(age=20).exists()

# delete()
Student.objects.filter(age=20).delete()

# update() - bulk update
Student.objects.filter(age=20).update(age=21)

# bulk_create() - create many at once
Student.objects.bulk_create([Student(name='A'), Student(name='B')])

# first() / last()
Student.objects.first()
Student.objects.last()

# aggregate()
from django.db.models import Avg
Student.objects.aggregate(avg_age=Avg('age'))
```

---

### Operators that return new QuerySets

```python
# AND (&)
Student.objects.filter(age=20) & Student.objects.filter(name='Ali')

# OR (|)
Student.objects.filter(age=20) | Student.objects.filter(name='Ali')

# NOT (~) - with Q objects
from django.db.models import Q
Student.objects.filter(~Q(age=20))
```

---

### Field Lookups

Field lookups go after a `__` (double underscore):

```python
# exact (default)
Student.objects.filter(name__exact='Ali')
Student.objects.filter(name='Ali')       # same thing

# iexact - case insensitive
Student.objects.filter(name__iexact='ali')

# contains
Student.objects.filter(name__contains='li')

# icontains - case insensitive
Student.objects.filter(name__icontains='li')

# startswith / endswith
Student.objects.filter(name__startswith='A')
Student.objects.filter(name__endswith='i')

# gt / gte / lt / lte
Student.objects.filter(age__gt=18)
Student.objects.filter(age__gte=18)
Student.objects.filter(age__lt=18)
Student.objects.filter(age__lte=18)

# in
Student.objects.filter(age__in=[18, 20, 22])

# range
Student.objects.filter(age__range=(18, 25))

# isnull
Student.objects.filter(email__isnull=True)

# date / year / month / day
Post.objects.filter(created_at__year=2024)
Post.objects.filter(created_at__month=1)
Post.objects.filter(created_at__date='2024-01-01')
```

---

### Q Object

> **Definition:** Q objects allow you to make complex queries with `AND`, `OR`, `NOT`.

```python
from django.db.models import Q

# OR
Student.objects.filter(Q(age=20) | Q(name='Ali'))

# AND
Student.objects.filter(Q(age=20) & Q(name='Ali'))

# NOT
Student.objects.filter(~Q(age=20))

# Combined
Student.objects.filter(Q(age__gt=18) & (Q(name='Ali') | Q(name='Ahmed')))
```

---

### Aggregation

> **Definition:** Aggregation calculates a single value from many records (like sum, avg, count).

```python
from django.db.models import Count, Sum, Avg, Max, Min

# aggregate() - returns a dict
result = Student.objects.aggregate(total=Count('id'), avg_age=Avg('age'))
# {'total': 50, 'avg_age': 21.5}
```

---

### Aggregation Functions

| Function | Description |
|---|---|
| `Count('field')` | Count records |
| `Sum('field')` | Sum of values |
| `Avg('field')` | Average value |
| `Max('field')` | Maximum value |
| `Min('field')` | Minimum value |

```python
from django.db.models import Count, Sum, Avg, Max, Min

Student.objects.aggregate(
    total=Count('id'),
    total_age=Sum('age'),
    avg_age=Avg('age'),
    oldest=Max('age'),
    youngest=Min('age'),
)
```

---

### Limiting QuerySets

```python
# First 5 records (LIMIT 5)
Student.objects.all()[:5]

# Records 5 to 10 (OFFSET 5 LIMIT 5)
Student.objects.all()[5:10]

# Single record by index (not recommended, use get())
Student.objects.all()[0]
```

> **Tip:** Negative indexing is NOT supported in QuerySets.

---

## 4. Custom User & Authentication

---

### Default User Model

Django's built-in `User` model has these fields by default:
- `username`, `password`, `email`, `first_name`, `last_name`
- `is_staff`, `is_active`, `is_superuser`
- `date_joined`, `last_login`

```python
from django.contrib.auth.models import User

user = User.objects.create_user(username='ali', password='pass123')
```

---

### Custom User Model using AbstractUser

> **Use this when** you want to add extra fields but keep all default behavior.

```python
# models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class CustomUser(AbstractUser):
    phone = models.CharField(max_length=15, blank=True)
    bio = models.TextField(blank=True)
```

```python
# settings.py
AUTH_USER_MODEL = 'myapp.CustomUser'
```

---

### Custom User Model using AbstractBaseUser

> **Use this when** you want full control (e.g., login with email instead of username).

```python
# models.py
from django.contrib.auth.models import AbstractBaseUser, PermissionsMixin, BaseUserManager
from django.db import models

class CustomUserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        return self.create_user(email, password, **extra_fields)

class CustomUser(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(unique=True)
    name = models.CharField(max_length=100)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)

    objects = CustomUserManager()

    USERNAME_FIELD = 'email'     # login field
    REQUIRED_FIELDS = ['name']  # required for createsuperuser

    def __str__(self):
        return self.email
```

---

### Custom User Manager

> A Manager controls how you create users. Always override it with `AbstractBaseUser`.

- `create_user()` → Normal user
- `create_superuser()` → Admin user

---

### AUTH_USER_MODEL Setting

```python
# settings.py
AUTH_USER_MODEL = 'myapp.CustomUser'
```

> **Important:** Set this **before** the first migration. Changing it later is very difficult.

---

### Authentication Functions

```python
from django.contrib.auth import authenticate, login, logout

# authenticate - check credentials
user = authenticate(request, username='ali', password='pass123')

# login - log the user in (creates session)
login(request, user)

# logout - log the user out (clears session)
logout(request)
```

```python
# views.py
def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        if user is not None:
            login(request, user)
            return redirect('home')
    return render(request, 'login.html')
```

---

### Permissions and Groups

```python
# Check permission
request.user.has_perm('myapp.add_post')

# Add permission
from django.contrib.auth.models import Permission
perm = Permission.objects.get(codename='add_post')
user.user_permissions.add(perm)

# Groups
from django.contrib.auth.models import Group
group = Group.objects.get(name='Editors')
user.groups.add(group)
```

---

## 5. Built-in Authentication Tools

---

### Built-in Authentication

Django provides a complete authentication system out of the box:
- Login / Logout
- Password change / reset
- Session management

**Setup in urls.py:**
```python
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('accounts/', include('django.contrib.auth.urls')),
]
```

---

### login_required Decorator

> Redirects unauthenticated users to login page.

```python
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return render(request, 'dashboard.html')
```

```python
# settings.py
LOGIN_URL = '/login/'
LOGIN_REDIRECT_URL = '/dashboard/'
```

---

### staff_member_required Decorator

> Only allows staff members (`is_staff=True`).

```python
from django.contrib.admin.views.decorators import staff_member_required

@staff_member_required
def admin_panel(request):
    return render(request, 'admin_panel.html')
```

---

### permission_required Decorator

```python
from django.contrib.auth.decorators import permission_required

@permission_required('myapp.add_post')
def add_post(request):
    ...

# Raise 403 instead of redirect
@permission_required('myapp.add_post', raise_exception=True)
def add_post(request):
    ...
```

---

### Authentication Views

Django provides these views in `django.contrib.auth.views`:

| URL Name | View | Template Needed |
|---|---|---|
| `login` | `LoginView` | `registration/login.html` |
| `logout` | `LogoutView` | `registration/logged_out.html` |
| `password_change` | `PasswordChangeView` | `registration/password_change_form.html` |
| `password_change_done` | `PasswordChangeDoneView` | `registration/password_change_done.html` |
| `password_reset` | `PasswordResetView` | `registration/password_reset_form.html` |
| `password_reset_done` | `PasswordResetDoneView` | `registration/password_reset_done.html` |
| `password_reset_confirm` | `PasswordResetConfirmView` | `registration/password_reset_confirm.html` |
| `password_reset_complete` | `PasswordResetCompleteView` | `registration/password_reset_complete.html` |

---

### Built-in Forms

```python
from django.contrib.auth.forms import (
    AuthenticationForm,       # Login form
    UserCreationForm,         # Register form
    PasswordChangeForm,       # Change password
    PasswordResetForm,        # Reset password (sends email)
    SetPasswordForm,          # Set new password
)
```

---

### Auth Settings

```python
# settings.py
LOGIN_URL = '/accounts/login/'
LOGIN_REDIRECT_URL = '/'
LOGOUT_REDIRECT_URL = '/'
```

---

## 6. Register Model (Admin)

---

### admin.py File

The `admin.py` file is where you register your models so they appear in Django's admin panel.

---

### Register Model Without Model Admin

```python
# admin.py
from django.contrib import admin
from .models import Student

admin.site.register(Student)
```

---

### str Method

Always define `__str__` in your model so the admin shows meaningful names:

```python
class Student(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name
```

---

### Model Admin

`ModelAdmin` lets you customize how a model appears in admin.

---

### Model Admin without decorator

```python
from django.contrib import admin
from .models import Student

class StudentAdmin(admin.ModelAdmin):
    list_display = ['name', 'age', 'email']

admin.site.register(Student, StudentAdmin)
```

---

### Model Admin with decorator

```python
from django.contrib import admin
from .models import Student

@admin.register(Student)
class StudentAdmin(admin.ModelAdmin):
    list_display = ['name', 'age', 'email']
```

---

### Commonly used Model Admin attributes

```python
@admin.register(Student)
class StudentAdmin(admin.ModelAdmin):
    list_display = ['name', 'age', 'email']        # columns to show
    list_filter = ['age', 'city']                  # sidebar filters
    search_fields = ['name', 'email']              # search box
    ordering = ['name']                            # default ordering
    list_per_page = 20                             # records per page
    readonly_fields = ['created_at']               # non-editable fields
    fields = ['name', 'age', 'email']              # form fields order
    fieldsets = (                                  # grouped sections
        ('Basic Info', {'fields': ('name', 'email')}),
        ('Details', {'fields': ('age', 'city')}),
    )
    list_editable = ['age']                        # inline edit in list
    date_hierarchy = 'created_at'                  # date navigation
    actions = ['make_active']                      # custom actions
    prepopulated_fields = {'slug': ('name',)}      # auto-fill slug
```

---

## 7. Django Forms Basics

---

### Creating Django Form

```python
# forms.py
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
```

---

### Form API

Key attributes and methods:
- `form.is_valid()` → validates and returns True/False
- `form.cleaned_data` → dict of validated data
- `form.errors` → dict of errors
- `form.as_p()` → render as `<p>` tags
- `form.as_table()` → render as table
- `form.as_ul()` → render as `<ul>`

---

### Bound and Unbound Forms

- **Unbound**: No data submitted yet → used to render empty form
- **Bound**: Has submitted data → can be validated

```python
form = ContactForm()              # unbound
form = ContactForm(request.POST) # bound
```

---

### Creating Form Object

```python
# Unbound (display empty form)
form = ContactForm()

# Bound (with POST data)
form = ContactForm(request.POST)

# With initial values
form = ContactForm(initial={'name': 'Ali'})

# With prefix (multiple forms in one page)
form = ContactForm(prefix='contact')
```

---

### Display form to User

```python
# views.py
def contact_view(request):
    form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

---

### Using Form object in Template File

```html
<!-- contact.html -->
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Send</button>
</form>
```

---

## 8. Form Fields & Widgets

---

### Form Field

A form field handles validation and rendering of a single HTML input.

---

### Built-in Form Fields

| Field | HTML Input |
|---|---|
| `CharField` | text input |
| `EmailField` | email input |
| `IntegerField` | number input |
| `FloatField` | number input |
| `DecimalField` | number input |
| `BooleanField` | checkbox |
| `ChoiceField` | select dropdown |
| `MultipleChoiceField` | multi-select |
| `DateField` | date input |
| `DateTimeField` | datetime input |
| `FileField` | file input |
| `ImageField` | image input |
| `URLField` | url input |
| `SlugField` | text input |
| `UUIDField` | text input |
| `TypedChoiceField` | select with type coercion |

---

### Field Arguments

```python
name = forms.CharField(
    required=True,              # field is required
    label='Your Name',          # label text
    initial='Ali',              # default value
    help_text='Enter your name',
    widget=forms.TextInput(attrs={'class': 'form-control'}),
    error_messages={'required': 'Please enter your name'},
    validators=[my_validator],
    disabled=False,
    strip=True,                 # strip whitespace (CharField only)
    max_length=100,
    min_length=2,
)
```

---

### Widget

A Widget is the HTML representation of a field. It controls **how** the field is rendered in HTML.

```python
class MyForm(forms.Form):
    bio = forms.CharField(widget=forms.Textarea(attrs={'rows': 4, 'cols': 50}))
    password = forms.CharField(widget=forms.PasswordInput)
    hidden = forms.CharField(widget=forms.HiddenInput)
```

---

### Built-in Widgets

| Widget | HTML |
|---|---|
| `TextInput` | `<input type="text">` |
| `Textarea` | `<textarea>` |
| `EmailInput` | `<input type="email">` |
| `NumberInput` | `<input type="number">` |
| `PasswordInput` | `<input type="password">` |
| `CheckboxInput` | `<input type="checkbox">` |
| `Select` | `<select>` |
| `SelectMultiple` | `<select multiple>` |
| `RadioSelect` | `<input type="radio">` |
| `CheckboxSelectMultiple` | multiple checkboxes |
| `FileInput` | `<input type="file">` |
| `HiddenInput` | `<input type="hidden">` |
| `DateInput` | `<input type="date">` |
| `DateTimeInput` | `<input type="datetime-local">` |
| `SplitDateTimeWidget` | two inputs |

---

## 9. Form Rendering

---

### Form Rendering Options

```html
<!-- As paragraph tags -->
{{ form.as_p }}

<!-- As table rows -->
{{ form.as_table }}

<!-- As unordered list -->
{{ form.as_ul }}

<!-- As div (Django 4.1+) -->
{{ form.as_div }}
```

---

### Render Form Field Manually

```html
<form method="post">
    {% csrf_token %}

    <!-- Access each field individually -->
    <div>
        {{ form.name.label_tag }}
        {{ form.name }}
        {{ form.name.errors }}
        <small>{{ form.name.help_text }}</small>
    </div>

    <div>
        {{ form.email.label_tag }}
        {{ form.email }}
        {{ form.email.errors }}
    </div>

    <button type="submit">Submit</button>
</form>
```

---

## 10. Form Handling

---

### GET and POST

- `GET` → Retrieve data (display form)
- `POST` → Submit data (process form)

```python
def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # process data
            name = form.cleaned_data['name']
            return redirect('success')
    else:
        form = ContactForm()  # empty form
    return render(request, 'contact.html', {'form': form})
```

---

### Cross Site Request Forgery

> **CSRF** is an attack where a malicious website tricks a logged-in user into submitting a form on your site without their knowledge.

**Example attack:** A user is logged into your banking site. An attacker tricks them into clicking a hidden form that transfers money.

---

### Django CSRF Protection

Django generates a unique token for each session. Every POST form must include this token.

```html
<form method="post">
    {% csrf_token %}  <!-- adds hidden input with token -->
    ...
</form>
```

For AJAX:
```javascript
// Get CSRF token from cookie
function getCookie(name) {
    let cookieValue = null;
    if (document.cookie) {
        document.cookie.split(';').forEach(function(cookie) {
            if (cookie.trim().startsWith(name + '=')) {
                cookieValue = decodeURIComponent(cookie.trim().slice(name.length + 1));
            }
        });
    }
    return cookieValue;
}

fetch('/api/endpoint/', {
    method: 'POST',
    headers: {'X-CSRFToken': getCookie('csrftoken')},
    body: JSON.stringify(data)
});
```

---

## 11. Form Validation

---

### is_valid Method

```python
form = ContactForm(request.POST)
if form.is_valid():
    data = form.cleaned_data  # safe, validated data
else:
    errors = form.errors      # dict of errors
```

---

### Built-in Field Validation

Each field auto-validates:
- `EmailField` → checks valid email format
- `IntegerField` → checks it's a number
- `URLField` → checks valid URL
- `required=True` → checks field is not empty

---

### Custom Validation

Use `clean_<fieldname>()` method for per-field validation:

```python
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    age = forms.IntegerField()

    def clean_name(self):
        name = self.cleaned_data['name']
        if name.lower() == 'admin':
            raise forms.ValidationError("Name 'admin' is not allowed.")
        return name

    def clean_age(self):
        age = self.cleaned_data['age']
        if age < 18:
            raise forms.ValidationError("Must be 18 or older.")
        return age
```

Use `clean()` for cross-field validation:

```python
    def clean(self):
        cleaned_data = super().clean()
        password = cleaned_data.get('password')
        confirm = cleaned_data.get('confirm_password')
        if password != confirm:
            raise forms.ValidationError("Passwords do not match.")
        return cleaned_data
```

---

### Built-in Validators

```python
from django.core.validators import (
    MinValueValidator,
    MaxValueValidator,
    MinLengthValidator,
    MaxLengthValidator,
    RegexValidator,
    EmailValidator,
    URLValidator,
)

age = forms.IntegerField(validators=[MinValueValidator(18), MaxValueValidator(100)])
username = forms.CharField(validators=[RegexValidator(r'^[a-zA-Z0-9]+$', 'Only alphanumeric characters.')])
```

---

### Custom Validators

```python
from django.core.exceptions import ValidationError

def validate_no_spaces(value):
    if ' ' in value:
        raise ValidationError("No spaces allowed.")

class MyForm(forms.Form):
    username = forms.CharField(validators=[validate_no_spaces])
```

---

## 12. Form Errors

---

### Form Errors

```python
form = ContactForm(request.POST)
if not form.is_valid():
    print(form.errors)  # dict of all errors
```

---

### Field Errors

```python
# In view
form.errors['name']  # errors for 'name' field

# In template
{{ form.name.errors }}
```

---

### Non Field Errors

Errors not tied to any specific field (from `clean()` method):

```python
# In view
form.non_field_errors()

# In template
{{ form.non_field_errors }}
```

---

### Styling Django Form Errors

```html
<!-- Template -->
{% if form.non_field_errors %}
    <div class="alert alert-danger">
        {% for error in form.non_field_errors %}
            <p>{{ error }}</p>
        {% endfor %}
    </div>
{% endif %}

<div class="{% if form.name.errors %}has-error{% endif %}">
    {{ form.name.label_tag }}
    {{ form.name }}
    {% for error in form.name.errors %}
        <span class="error-text">{{ error }}</span>
    {% endfor %}
</div>
```

---

## 13. Model Forms

---

### Model Form

> A ModelForm automatically creates a form from a Model — no need to redefine fields.

```python
# models.py
class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    email = models.EmailField()

# forms.py
from django import forms
from .models import Student

class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = '__all__'  # all fields
```

```python
# views.py
def add_student(request):
    if request.method == 'POST':
        form = StudentForm(request.POST)
        if form.is_valid():
            form.save()  # saves directly to DB
            return redirect('student-list')
    else:
        form = StudentForm()
    return render(request, 'add_student.html', {'form': form})
```

---

### Selecting Fields

```python
class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = ['name', 'email']       # only these fields
        # OR
        exclude = ['created_at']         # all except these

        # Custom widgets
        widgets = {
            'name': forms.TextInput(attrs={'class': 'form-control'}),
        }

        # Custom labels
        labels = {
            'name': 'Full Name',
        }

        # Help text
        help_texts = {
            'name': 'Enter your full name.',
        }
```

---

### Model Form Inheritance

```python
class BaseForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = ['name']

class ExtendedForm(BaseForm):
    class Meta(BaseForm.Meta):
        fields = BaseForm.Meta.fields + ['email', 'age']
```

---

## 14. Function vs Class Based Views

---

### Function Based View

```python
# views.py
from django.shortcuts import render

def student_list(request):
    students = Student.objects.all()
    return render(request, 'student_list.html', {'students': students})
```

```python
# urls.py
path('students/', views.student_list, name='student-list'),
```

---

### Class Based View

```python
from django.views import View
from django.shortcuts import render

class StudentListView(View):
    def get(self, request):
        students = Student.objects.all()
        return render(request, 'student_list.html', {'students': students})
```

```python
# urls.py
path('students/', views.StudentListView.as_view(), name='student-list'),
```

---

### FBV vs CBV Comparison

| Feature | FBV | CBV |
|---|---|---|
| Simplicity | Simpler to read | More complex |
| Reusability | Low | High (inheritance) |
| Built-in generics | No | Yes |
| HTTP methods | Manual if/else | Separate methods (get, post) |
| Decorators | Easy | Needs `method_decorator` |
| Best for | Simple, custom logic | CRUD, standard patterns |

---

## 15. Base Class-Based Views

---

### View

Base class for all class-based views.

```python
from django.views import View
from django.http import HttpResponse

class MyView(View):
    def get(self, request):
        return HttpResponse("GET request")

    def post(self, request):
        return HttpResponse("POST request")
```

---

### TemplateView

> Renders a template. No model needed.

```python
from django.views.generic import TemplateView

class AboutView(TemplateView):
    template_name = 'about.html'
    extra_context = {'title': 'About Us'}  # optional extra context

# Or pass context dynamically
class AboutView(TemplateView):
    template_name = 'about.html'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['title'] = 'About Us'
        return context
```

---

### RedirectView

> Redirects to a URL.

```python
from django.views.generic import RedirectView

class OldPageView(RedirectView):
    url = '/new-page/'
    permanent = False  # 302 redirect (True = 301)

# Dynamic redirect
class DynamicRedirect(RedirectView):
    pattern_name = 'student-detail'

    def get_redirect_url(self, *args, **kwargs):
        return f'/students/{kwargs["pk"]}/'
```

---

## 16. Generic Class-Based Views

---

### ListView

> Shows a list of objects.

```python
from django.views.generic import ListView

class StudentListView(ListView):
    model = Student
    template_name = 'student_list.html'   # default: student_list.html
    context_object_name = 'students'      # default: object_list
    paginate_by = 10                       # optional pagination
    ordering = ['name']

    def get_queryset(self):
        return Student.objects.filter(is_active=True)
```

---

### DetailView

> Shows a single object.

```python
from django.views.generic import DetailView

class StudentDetailView(DetailView):
    model = Student
    template_name = 'student_detail.html'
    context_object_name = 'student'    # default: object
```

```python
# urls.py
path('students/<int:pk>/', StudentDetailView.as_view()),
```

---

### FormView

> Shows and handles a form (not a model form).

```python
from django.views.generic.edit import FormView

class ContactFormView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = '/thank-you/'

    def form_valid(self, form):
        # process form data
        return super().form_valid(form)
```

---

### CreateView

> Shows a form and creates a new record.

```python
from django.views.generic.edit import CreateView
from django.urls import reverse_lazy

class StudentCreateView(CreateView):
    model = Student
    fields = ['name', 'age', 'email']
    template_name = 'student_form.html'
    success_url = reverse_lazy('student-list')
```

---

### UpdateView

> Shows a form with existing data and updates it.

```python
from django.views.generic.edit import UpdateView

class StudentUpdateView(UpdateView):
    model = Student
    fields = ['name', 'age', 'email']
    template_name = 'student_form.html'
    success_url = reverse_lazy('student-list')
```

---

### DeleteView

> Confirms and deletes a record.

```python
from django.views.generic.edit import DeleteView

class StudentDeleteView(DeleteView):
    model = Student
    template_name = 'student_confirm_delete.html'
    success_url = reverse_lazy('student-list')
```

---

## 17. Decorators in Views

---

### Method Decorator

> Apply function-based decorators to class-based views.

```python
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.views.generic import ListView

@method_decorator(login_required, name='dispatch')
class StudentListView(ListView):
    model = Student
    template_name = 'student_list.html'
```

---

### Decorating in URLconf

```python
# urls.py
from django.contrib.auth.decorators import login_required
from .views import StudentListView

urlpatterns = [
    path('students/', login_required(StudentListView.as_view())),
]
```

---

### Decorating in the Class

```python
from django.utils.decorators import method_decorator

class StudentListView(ListView):
    model = Student

    @method_decorator(login_required)
    def dispatch(self, *args, **kwargs):
        return super().dispatch(*args, **kwargs)
```

---

## 18. Utilities

---

### JSONResponse

```python
from django.http import JsonResponse

def api_view(request):
    data = {'name': 'Ali', 'age': 20}
    return JsonResponse(data)

# Return a list (safe=False required)
def api_list(request):
    data = [{'name': 'Ali'}, {'name': 'Ahmed'}]
    return JsonResponse(data, safe=False)

# Custom status code
return JsonResponse({'error': 'Not found'}, status=404)
```

---

### reverse and reverse_lazy

> `reverse()` builds a URL from a URL name (instead of hardcoding).

```python
from django.urls import reverse, reverse_lazy

# reverse() - used in views
url = reverse('student-detail', kwargs={'pk': 1})
# Returns: '/students/1/'

# reverse_lazy() - used in class-based views (evaluated lazily)
success_url = reverse_lazy('student-list')
```

---

### Namespace

> Namespaces prevent URL name conflicts between apps.

```python
# app_name = 'students' in students/urls.py
app_name = 'students'
urlpatterns = [
    path('', views.StudentListView.as_view(), name='list'),
    path('<int:pk>/', views.StudentDetailView.as_view(), name='detail'),
]

# In main urls.py
path('students/', include('students.urls', namespace='students')),

# Usage
reverse('students:list')
reverse('students:detail', kwargs={'pk': 1})

# In template
{% url 'students:list' %}
{% url 'students:detail' pk=1 %}
```

---

## 19. Query Optimization

---

### select_related and prefetch_related

**The N+1 Problem:** If you fetch 100 posts and access `post.author` in a loop, Django makes 101 DB queries!

```python
# BAD (N+1 queries)
posts = Post.objects.all()
for post in posts:
    print(post.author.name)  # 1 query per post!

# GOOD with select_related (JOIN — for ForeignKey, OneToOne)
posts = Post.objects.select_related('author').all()

# GOOD with prefetch_related (separate query + Python join — for ManyToMany, reverse FK)
students = Student.objects.prefetch_related('courses').all()
```

| Method | Use for | Query type |
|---|---|---|
| `select_related` | ForeignKey, OneToOne | SQL JOIN |
| `prefetch_related` | ManyToMany, reverse FK | Separate queries |

---

### Annotate

> Adds a calculated field to each object in the QuerySet.

```python
from django.db.models import Count, Avg

# Add course count to each student
students = Student.objects.annotate(course_count=Count('courses'))
for s in students:
    print(s.name, s.course_count)

# Average grade per student
students = Student.objects.annotate(avg_grade=Avg('grades__score'))
```

**`annotate` vs `aggregate`:**
- `annotate` → adds field to **each** object
- `aggregate` → returns a **single** value for the whole queryset

---

## 20. Pagination

```python
from django.core.paginator import Paginator

def student_list(request):
    students = Student.objects.all()
    paginator = Paginator(students, 10)          # 10 per page
    page_number = request.GET.get('page')        # ?page=1
    page_obj = paginator.get_page(page_number)   # handles invalid pages
    return render(request, 'student_list.html', {'page_obj': page_obj})
```

```html
<!-- Template -->
{% for student in page_obj %}
    <p>{{ student.name }}</p>
{% endfor %}

<!-- Pagination controls -->
{% if page_obj.has_previous %}
    <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
{% endif %}

Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}

{% if page_obj.has_next %}
    <a href="?page={{ page_obj.next_page_number }}">Next</a>
{% endif %}
```

**With ListView (built-in):**
```python
class StudentListView(ListView):
    model = Student
    paginate_by = 10
    template_name = 'student_list.html'
```

---

## 21. Caching

---

### How Cache works

1. User makes a request
2. Django checks if result is cached
3. If yes → return cached result (fast!)
4. If no → process request, save to cache, return result

---

### How to Implement Caching

```python
# settings.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}
```

---

### Cache Argument Types

```python
cache.set('key', value, timeout=300)  # timeout in seconds
# timeout=None → cache forever
# timeout=0 → don't cache
```

---

### Per-site cache

> Caches every page automatically.

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',   # FIRST
    ...
    'django.middleware.cache.FetchFromCacheMiddleware', # LAST
]
CACHE_MIDDLEWARE_SECONDS = 600
```

---

### Per-view cache

```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)  # cache for 15 minutes
def my_view(request):
    ...

# In URLconf
path('students/', cache_page(60 * 15)(student_list)),
```

---

### Template Fragment Caching

```html
{% load cache %}

{% cache 500 sidebar %}
    <!-- This block is cached for 500 seconds -->
    {% for student in students %}
        {{ student.name }}
    {% endfor %}
{% endcache %}
```

---

### Caching Backends

#### Database Caching
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'LOCATION': 'my_cache_table',
    }
}
# Run: python manage.py createcachetable
```

#### Filesystem Caching
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
    }
}
```

#### Local Memory Caching
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake',
    }
}
```

#### Dummy Caching
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache',
    }
}
# Does nothing — for development
```

---

## 22. Cookies

---

### Django Cookies

> Cookies are small pieces of data stored in the **user's browser**. They persist across requests.

---

### Creating Cookies

```python
def set_cookie_view(request):
    response = HttpResponse("Cookie set!")
    response.set_cookie('username', 'ali')
    return response
```

---

### Reading Cookies

```python
def read_cookie_view(request):
    username = request.COOKIES.get('username', 'Guest')
    return HttpResponse(f"Hello, {username}")
```

---

### Replace or Append Cookies

```python
response.set_cookie('username', 'ahmed')  # replaces existing
```

---

### Deleting Cookies

```python
def delete_cookie_view(request):
    response = HttpResponse("Cookie deleted!")
    response.delete_cookie('username')
    return response
```

---

### Signed Cookies

> Signed cookies are tamper-proof. Django signs them with `SECRET_KEY`.

```python
# Set signed cookie
response.set_signed_cookie('user_id', '42', salt='my-salt')

# Read signed cookie
user_id = request.get_signed_cookie('user_id', salt='my-salt', default=None)
```

---

### Security issues & Limitations

**Security Issues:**
- Cookies can be stolen (use `httponly=True`, `secure=True`)
- Don't store sensitive data in plain cookies

**Secure cookie options:**
```python
response.set_cookie(
    'username', 'ali',
    max_age=3600,       # seconds until expiry
    expires=None,       # specific datetime
    httponly=True,      # JS cannot access
    secure=True,        # HTTPS only
    samesite='Lax',     # CSRF protection
)
```

**Limitations:**
- Max size: ~4KB per cookie
- Max cookies per domain: ~20-50
- Users can delete/block cookies

---

## 23. Sessions

> Sessions store data on the **server** (not the browser). Browser only holds a session ID in a cookie.

---

### Session Types

#### Database backed sessions (default)
```python
# settings.py
SESSION_ENGINE = 'django.contrib.sessions.backends.db'
# Run: python manage.py migrate
```

#### File based sessions
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.file'
SESSION_FILE_PATH = '/tmp/django_sessions'
```

#### Cookie based sessions
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies'
# Data stored IN the cookie (max ~4KB, signed)
```

#### Cached Sessions
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
# Fast but data lost if cache clears

# Cache + DB fallback
SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'
```

---

### Using Session in views

```python
def set_session(request):
    request.session['username'] = 'ali'
    request.session['cart_count'] = 3
    return HttpResponse("Session set")

def get_session(request):
    username = request.session.get('username', 'Guest')
    return HttpResponse(f"Hello, {username}")

def delete_session_key(request):
    try:
        del request.session['username']
    except KeyError:
        pass
    return HttpResponse("Key deleted")

def clear_all_session(request):
    request.session.flush()  # delete all session data + cookie
    return HttpResponse("Session cleared")
```

---

### Session Methods

```python
request.session['key'] = value         # set
request.session.get('key', default)    # get
del request.session['key']             # delete key
request.session.flush()                # delete everything
request.session.clear()                # clear data, keep session ID
request.session.keys()                 # all keys
request.session.items()                # all items
request.session.get_expiry_age()       # seconds until expiry
request.session.set_expiry(300)        # set expiry in seconds
request.session.set_expiry(0)          # expire at browser close
```

---

### Session Settings

```python
# settings.py
SESSION_COOKIE_AGE = 1209600       # 2 weeks (default)
SESSION_EXPIRE_AT_BROWSER_CLOSE = False
SESSION_SAVE_EVERY_REQUEST = False
SESSION_COOKIE_SECURE = True       # HTTPS only
SESSION_COOKIE_HTTPONLY = True     # No JS access
SESSION_COOKIE_NAME = 'sessionid'
```

---

## 24. Middleware

---

### How Middleware Works

Middleware is a hook system between the request/response cycle.

```
Request → Middleware 1 → Middleware 2 → ... → View
Response ← Middleware 1 ← Middleware 2 ← ... ← View
```

---

### Function based Middleware

```python
def simple_middleware(get_response):
    # One-time setup code

    def middleware(request):
        # Code BEFORE view
        print("Before view")

        response = get_response(request)

        # Code AFTER view
        print("After view")
        return response

    return middleware
```

---

### Class based Middleware

```python
class SimpleMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        # One-time setup

    def __call__(self, request):
        # Before view
        print("Before view")

        response = self.get_response(request)

        # After view
        print("After view")
        return response
```

---

### Activating Middleware

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'myapp.middleware.SimpleMiddleware',   # your custom middleware
    ...
]
```

> **Order matters!** Middleware runs top-to-bottom on request, bottom-to-top on response.

---

### Middleware Hooks

```python
class AdvancedMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        return response

    def process_view(self, request, view_func, view_args, view_kwargs):
        # Called just BEFORE the view
        pass

    def process_exception(self, request, exception):
        # Called when view raises an exception
        pass

    def process_template_response(self, request, response):
        # Called when view returns TemplateResponse
        return response
```

---

### TemplateResponse

```python
from django.template.response import TemplateResponse

def my_view(request):
    return TemplateResponse(request, 'template.html', {'key': 'value'})
```

---

### Built-in Middleware

| Middleware | Purpose |
|---|---|
| `SecurityMiddleware` | HTTPS, HSTS, XSS, etc. |
| `SessionMiddleware` | Session support |
| `CommonMiddleware` | URL normalization, etc. |
| `CsrfViewMiddleware` | CSRF protection |
| `AuthenticationMiddleware` | Attach user to request |
| `MessageMiddleware` | Flash messages |
| `XFrameOptionsMiddleware` | Clickjacking protection |
| `GZipMiddleware` | Compress responses |
| `LocaleMiddleware` | Language/translation |
| `CacheMiddleware` | Per-site caching |

---

## 25. Signals

> **Definition:** Signals let you run code automatically when something happens (like saving a model).

---

### Built-in Signals

| Signal | When it fires |
|---|---|
| `pre_save` | Before `model.save()` |
| `post_save` | After `model.save()` |
| `pre_delete` | Before `model.delete()` |
| `post_delete` | After `model.delete()` |
| `m2m_changed` | When M2M relation changes |
| `pre_migrate` | Before migration runs |
| `post_migrate` | After migration runs |
| `request_started` | Before each HTTP request |
| `request_finished` | After each HTTP request |

```python
from django.db.models.signals import post_save
from django.contrib.auth.models import User
from django.dispatch import receiver

@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```

---

### Custom Signals

---

### Defining Custom Signals

```python
# signals.py
from django.dispatch import Signal

order_completed = Signal()
```

---

### Sending Signals

```python
from .signals import order_completed

def complete_order(order):
    # ... complete order logic
    order_completed.send(sender=order.__class__, order=order)
```

---

### Disconnecting Signals

```python
# Connect
post_save.connect(create_profile, sender=User)

# Disconnect
post_save.disconnect(create_profile, sender=User)
```

**Register signals in `apps.py`:**
```python
class MyAppConfig(AppConfig):
    name = 'myapp'

    def ready(self):
        import myapp.signals  # loads the signal receivers
```

---

## 26. Django Security

---

### CSRF

Already covered in [Section 10](#django-csrf-protection).

---

### Authentication Security

- Always use `set_password()` — never store plain text passwords
- Use `check_password()` to verify passwords
- Use `PBKDF2` (default) or `bcrypt` for password hashing

```python
user.set_password('new_password')
user.check_password('new_password')  # returns True/False
```

---

### General Security Practices

```python
# settings.py
DEBUG = False                     # Never True in production
ALLOWED_HOSTS = ['yourdomain.com']

SECRET_KEY = os.environ.get('SECRET_KEY')  # never hardcode

# HTTPS settings
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True

# Clickjacking protection
X_FRAME_OPTIONS = 'DENY'

# Content type sniffing
SECURE_CONTENT_TYPE_NOSNIFF = True

# XSS protection header
SECURE_BROWSER_XSS_FILTER = True
```

---

## 27. Logging

> **Definition:** Logging records events in your application for debugging and monitoring.

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
    'root': {
        'handlers': ['console', 'file'],
        'level': 'INFO',
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'WARNING',
            'propagate': True,
        },
        'myapp': {
            'handlers': ['console', 'file'],
            'level': 'DEBUG',
        },
    },
}
```

**Using in views:**
```python
import logging

logger = logging.getLogger(__name__)

def my_view(request):
    logger.debug("Debug message")
    logger.info("Info message")
    logger.warning("Warning!")
    logger.error("Error occurred!")
    logger.critical("Critical error!")
```

**Log levels (low → high):**
`DEBUG` → `INFO` → `WARNING` → `ERROR` → `CRITICAL`

---

## 28. Static Files

---

### Creating Static Files

```
myapp/
  static/
    myapp/
      css/
        style.css
      js/
        script.js
      images/
        logo.png
```

---

### Using Static Files

```html
{% load static %}

<link rel="stylesheet" href="{% static 'myapp/css/style.css' %}">
<script src="{% static 'myapp/js/script.js' %}"></script>
<img src="{% static 'myapp/images/logo.png' %}" alt="Logo">
```

---

### Complete Example

**settings.py:**
```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']  # project-level static files
STATIC_ROOT = BASE_DIR / 'staticfiles'    # collectstatic destination
```

**style.css:**
```css
body {
    font-family: Arial, sans-serif;
    background-color: #f5f5f5;
}
```

**template:**
```html
<!DOCTYPE html>
<html>
<head>
    {% load static %}
    <link rel="stylesheet" href="{% static 'myapp/css/style.css' %}">
</head>
<body>
    <h1>Hello, Django!</h1>
</body>
</html>
```

---

## 29. Template Tags for Static

---

### load Template Tag

```html
{% load static %}
```
Must be at the top of template before using `{% static %}`.

---

### static Template Tag

```html
{% load static %}
<img src="{% static 'myapp/images/logo.png' %}">
```

---

### get_static_prefix Template Tag

Returns `STATIC_URL` value:

```html
{% load static %}
{% get_static_prefix as STATIC_PREFIX %}
<img src="{{ STATIC_PREFIX }}myapp/images/logo.png">
```

---

### get_media_prefix Template Tag

Returns `MEDIA_URL` value (for user-uploaded files):

```html
{% load static %}
{% get_media_prefix as MEDIA_PREFIX %}
<img src="{{ MEDIA_PREFIX }}uploads/profile.jpg">
```

---

## 30. Settings for Static Files

---

### STATIC_URL

```python
STATIC_URL = '/static/'  # URL prefix for static files
```

---

### STATIC_ROOT

```python
STATIC_ROOT = BASE_DIR / 'staticfiles'
# Where collectstatic puts all files (for production)
```

---

### STATICFILES_DIRS

```python
STATICFILES_DIRS = [
    BASE_DIR / 'static',     # project-level static folder
]
```

---

### Collectstatic

```bash
python manage.py collectstatic
```

Copies all static files from all apps + `STATICFILES_DIRS` into `STATIC_ROOT`.

**Use in production:** Serve `STATIC_ROOT` via Nginx/Apache/WhiteNoise.

**WhiteNoise (simple production setup):**
```bash
pip install whitenoise
```
```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # add here
    ...
]
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

*End of Django Complete Notes*
