# Django

# ما هي Django؟

ـ Django هو web framework مكتوب بلغة Python، يساعدك على بناء تطبيقات ويب بسرعة وكفاءة. Django يتبع نمط MTV (Model-Template-View) وهو مشابه لـ MVC في frameworks أخرى.

ـ Django يأتي مع مجموعة كبيرة من المكونات الجاهزة التي تسهل عملية التطوير، وهنا شرح للمكونات الأساسية:

## المكونات الأساسية في Django

### 1. ـ django.db

يحتوي على كل ما يتعلق بقاعدة البيانات والـ ORM

- ـ `django.db.models`: لإنشاء Models (الجداول) والـ Fields
- ـ `django.db.models.query`: لإنشاء استعلامات QuerySets
- ـ `django.db.connection`: للاتصال المباشر بقاعدة البيانات
- ـ `django.db.migrations`: لإدارة التغييرات على قاعدة البيانات

### 2.ـ django.urls

لإدارة URLs والـ routing في التطبيق

- ـ `path()`: لتحديد URL pattern عادي
- ـ `re_path()`: لتحديد URL pattern باستخدام regular expressions
- ـ `include()`: لتضمين URLs من ملف آخر

```python
from django.urls import path, include
from . import views

urlpatterns = [
    path('products/', views.product_list),
    path('products/<int:id>/', views.product_detail),
    path('api/', include('api.urls')),
]
```

### 3. ـ django.views

لإنشاء Views التي تعالج الطلبات وترجع الاستجابات

- ـ `django.views.generic`: Views جاهزة مثل ListView, DetailView, CreateView
- ـ `django.views.decorators`: Decorators مثل @login_required, @csrf_exempt

```python
from django.views.generic import ListView
from .models import Product

class ProductListView(ListView):
    model = Product
    template_name = 'products.html'
    context_object_name = 'products'
```

### 4. ـ django.forms

لإنشاء وإدارة Forms

- ـ `forms.Form`: لإنشاء form عادي
- ـ `forms.ModelForm`: لإنشاء form من Model
- ـ `forms.fields`: أنواع الحقول المختلفة (CharField, EmailField, etc.)
- ـ `forms.widgets`: لتخصيص شكل الحقول في HTML

```python
from django import forms
from .models import Product

class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'price', 'description']
        widgets = {
            'description': forms.Textarea(attrs={'rows': 4}),
        }
```

### 5. ـ django.template

لإدارة Templates (ملفات HTML)

- ـ `django.template.loader`: لتحميل templates
- ـ`django.template.response`: لإرجاع template كاستجابة
- ـ Template tags و filters: مثل {% for %}, {% if %}, {{ variable|filter }}

### 6. ـ django.contrib.auth

نظام المصادقة والصلاحيات المدمج

- ـ `User model`: Model جاهز للمستخدمين
- ـ `authenticate()`: للتحقق من بيانات المستخدم
- ـ `login()`, `logout()`: لتسجيل الدخول والخروج
- ـ `@login_required`: decorator يطلب تسجيل الدخول
- ـ `permissions`: نظام الصلاحيات

### 7.ـ django.contrib.admin

لوحة تحكم إدارية جاهزة تلقائياً

- واجهة لإدارة البيانات بدون كتابة كود
- يمكن تخصيصها بسهولة

```python
from django.contrib import admin
from .models import Product

@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    list_display = ['name', 'price', 'created_at']
    search_fields = ['name']
    list_filter = ['created_at']
```

### 8. ـ django.http

للتعامل مع HTTP requests و responses

- ـ `HttpResponse`: لإرجاع استجابة نصية
- ـ `JsonResponse`: لإرجاع JSON
- ـ `HttpResponseRedirect`: لإعادة التوجيه
- ـ `Http404`: لإظهار صفحة 404
- ـ `FileResponse`: لإرجاع ملف
- ـ `StreamingHttpResponse`: لإرجاع استجابة streaming

### 9. ـ django.shortcuts

مجموعة من الدوال المساعدة التي تسهل عمليات شائعة

- ـ`render()`: لعرض template مع context
- ـ `redirect()`: لإعادة التوجيه إلى URL أو view
- ـ `get_object_or_404()`: للحصول على object أو إظهار 404
- ـ `get_list_or_404()`: للحصول على list أو إظهار 404

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Product

def product_detail(request, id):
    product = get_object_or_404(Product, id=id)
    return render(request, 'product.html', {'product': product})
```

### 10. ـ django.core

يحتوي على مكونات أساسية مختلفة

- ـ `django.core.mail`: لإرسال البريد الإلكتروني
- ـ `django.core.cache`: لإدارة الـ caching
- ـ `django.core.validators`: validators جاهزة للحقول
- ـ `django.core.paginator`: لتقسيم البيانات إلى صفحات
- ـ `django.core.serializers`: لتحويل Models إلى JSON/XML
- ـ `django.core.files`: للتعامل مع الملفات

### 11. ـ django.middleware

ـ Middleware هي classes تعالج requests و responses

- ـ `SecurityMiddleware`: لتحسين الأمان
- ـ `SessionMiddleware`: لإدارة الجلسات
- ـ `AuthenticationMiddleware`: لربط المستخدمين بالـ requests
- ـ `CsrfViewMiddleware`: للحماية من CSRF attacks
- ـ `MessageMiddleware`: لإظهار رسائل للمستخدم

### 12. ـ django.contrib.messages

لإظهار رسائل مؤقتة للمستخدم

- ـ `messages.success()`: رسالة نجاح
- ـ `messages.error()`: رسالة خطأ
- ـ `messages.warning()`: رسالة تحذير
- ـ `messages.info()`: رسالة معلومات

```python
from django.contrib import messages
from django.shortcuts import redirect

def my_view(request):
    messages.success(request, 'تم الحفظ بنجاح!')
    return redirect('home')
```

### 13. ـdjango.contrib.staticfiles

لإدارة الملفات الثابتة (CSS, JavaScript, صور)

- ـ `collectstatic`: أمر لجمع كل الملفات الثابتة في مكان واحد
- ـ `STATIC_URL`: URL الأساسي للملفات الثابتة
- ـ `STATIC_ROOT`: المسار الذي تُجمع فيه الملفات الثابتة
- ـ `STATICFILES_DIRS`: مجلدات إضافية للملفات الثابتة
- ـ `STATICFILES_FINDERS`: طرق البحث عن الملفات الثابتة

### 14. ـ django.contrib.sessions

لإدارة الجلسات (Sessions)

- ـ `request.session`: للوصول إلى session الحالية
- ـ `session.set_expiry()`: لتحديد وقت انتهاء الجلسة
- ـ `session.flush()`: لحذف الجلسة
- يمكن حفظ Sessions في قاعدة البيانات، ملفات، أو cache

### 15. ـ django.contrib.contenttypes

للتعامل مع أنواع المحتوى المختلفة في التطبيق

- ـ `ContentType model`: يحفظ معلومات عن كل Model
- ـ `GenericForeignKey`: لإنشاء علاقة مع أي Model
- مفيد لإنشاء ميزات عامة مثل Comments أو Ratings

### 16. ـ django.test

لكتابة وتشغيل الاختبارات

- ـ `TestCase`منه: class أساسي لكتابة الاختبارات
- ـ `Client`: لمحاكاة requests في الاختبارات
- ـ `TransactionTestCase`: للاختبارات التي تحتاج transactions
- ـ `SimpleTestCase`: للاختبارات التي لا تحتاج قاعدة بيانات

```python
from django.test import TestCase
from .models import Product

class ProductTestCase(TestCase):
    def setUp(self):
        Product.objects.create(name="Test Product", price=100)
    
    def test_product_creation(self):
        product = Product.objects.get(name="Test Product")
        self.assertEqual(product.price, 100)
```

### 17. ـ django.utils

مجموعة من الأدوات المساعدة المفيدة

- ـ `django.utils.timezone`: للتعامل مع التواريخ والأوقات بطريقة صحيحة
- ـ `django.utils.translation`: للترجمة والتعدد اللغوي
- ـ `django.utils.text`: دوال مثل slugify()
- ـ `django.utils.html`: للتعامل مع HTML بأمان (escape, format_html)
- ـ `django.utils.decorators`: decorators مساعدة
- ـ `django.utils.encoding`: للتعامل مع encodings

## نصائح عامة

- **استخدم البيئة الافتراضية (Virtual Environment):** دائماً استخدم venv لعزل مشروعك
- **اقرأ الوثائق الرسمية:** وثائق Django شاملة ومفصلة جداً
- **استخدم Django Debug Toolbar:** أداة رائعة لتحليل الأداء أثناء التطوير
- **اتبع best practices:** مثل استخدام environment variables للإعدادات الحساسة
- **اكتب اختبارات:** الاختبارات تساعد في اكتشاف الأخطاء مبكراً
- **استخدم Django REST Framework:** إذا كنت تبني API