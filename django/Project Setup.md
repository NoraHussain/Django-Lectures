# Project Setup

## البدء بمشروع Django جديد

### 1. تثبيت Django

أولاً، تأكد من تثبيت Python على البيئة الافتراضية.

### 2. إنشاء Project جديد

لإنشاء مشروع Django جديد، استخدم الأمر التالي:

```bash
django-admin startproject myproject
```

هذا الأمر سينشئ مجلد المشروع بالهيكل التالي:

```
myproject/
    manage.py
    myproject/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

### 3. إنشاء App جديد

داخل مجلد المشروع، يمكنك إنشاء App باستخدام:

```bash
cd myproject
python manage.py startapp myapp
```

هذا سينشئ مجلد App بالهيكل التالي:

```
myapp/
    __init__.py
    admin.py
    apps.py
    models.py
    tests.py
    views.py
    migrations/
```

### 4. تسجيل App في المشروع

بعد إنشاء App، يجب تسجيله في ملف `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp',  # أضف App الخاص بك هنا
]
```

### 5. الإعدادات الأساسية في [settings.py](http://settings.py)

### أ. إعدادات Database

Django يستخدم SQLite افتراضياً. يمكنك تغيير إعدادات قاعدة البيانات:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

### ب. إعدادات Language و Timezone

```python
LANGUAGE_CODE = 'ar'  # للغة العربية

TIME_ZONE = 'Africa/Cairo'  # أو المنطقة الزمنية المناسبة لك

USE_I18N = True

USE_TZ = True
```

### ج. إعدادات Static Files

حدد مسار الملفات الثابتة (CSS, JavaScript, Images):

```python
STATIC_URL = '/static/'

STATICFILES_DIRS = [
    BASE_DIR / 'static',
]

STATIC_ROOT = BASE_DIR / 'staticfiles'
```

### د. إعدادات Media Files

لتخزين الملفات التي يرفعها المستخدمون:

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

### 6. إجراء Migration

بعد إعداد قاعدة البيانات، قم بتنفيذ الأوامر التالية:

```bash
python manage.py makemigrations
python manage.py migrate
```

### 7. إنشاء Superuser

لإنشاء حساب مدير للوصول إلى لوحة التحكم:

```bash
python manage.py createsuperuser
```

### 8. تشغيل Development Server

لتشغيل المشروع محلياً:

```bash
python manage.py runserver
```

يمكنك الآن الوصول إلى المشروع عبر: `http://127.0.0.1:8000/`

ولوحة التحكم عبر: `http://127.0.0.1:8000/admin/`