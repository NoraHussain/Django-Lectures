# URL Routing

# شرح URL Routing في Django

يعتبر URL Routing من المفاهيم الأساسية في Django، حيث يربط بين URLs والـ Views التي تعالج الطلبات وترجع الاستجابات.

## 1. أساسيات URL Routing

في Django، يتم تعريف URLs في ملف `urls.py`. يحتوي هذا الملف على قائمة `urlpatterns` التي تحدد كيفية ربط URLs بالـ Views.

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('about/', views.about, name='about'),
    path('contact/', views.contact, name='contact'),
]
```

## 2. الدوال الأساسية في URL Routing

### path()

الدالة الأكثر استخداماً لتحديد URL patterns:

```python
path(route, view, kwargs=None, name=None)
```

- **route:** نمط URL (string)
- **view:** الـ View function أو class-based view
- **kwargs:** معاملات إضافية (اختياري)
- **name:** اسم فريد للـ URL (اختياري)

### re_path()

تستخدم Regular Expressions لأنماط URLs أكثر تعقيداً:

```python
from django.urls import re_path

urlpatterns = [
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
]
```

### include()

تستخدم لتضمين URL configurations من تطبيقات أخرى:

```python
from django.urls import path, include

urlpatterns = [
    path('blog/', include('blog.urls')),
    path('shop/', include('shop.urls')),
]
```

## 3. أنواع Views

### Function-Based Views (FBV)

دوال Python عادية تستقبل HttpRequest وترجع HttpResponse:

```python
from django.http import HttpResponse

def home(request):
    return HttpResponse("Welcome to Home Page")

def about(request):
    return render(request, 'about.html')

```

### Class-Based Views (CBV)

Classes توفر طريقة أكثر تنظيماً لكتابة Views:

```python
from django.views import View
from django.views.generic import ListView, DetailView

class HomeView(View):
    def get(self, request):
        return HttpResponse("Home Page")
    
    def post(self, request):
        return HttpResponse("POST request")

# استخدامها في urls.py
path('home/', HomeView.as_view(), name='home')

```

### Generic Class-Based Views

Views جاهزة لحالات الاستخدام الشائعة:

- **ListView:** عرض قائمة من الكائنات
- **DetailView:** عرض تفاصيل كائن واحد
- **CreateView:** إنشاء كائن جديد
- **UpdateView:** تحديث كائن موجود
- **DeleteView:** حذف كائن
- **TemplateView:** عرض template بسيط
- **RedirectView:** إعادة توجيه
- **FormView:** معالجة Forms

```python
from django.views.generic import ListView
from .models import Article

class ArticleListView(ListView):
    model = Article
    template_name = 'articles.html'
    context_object_name = 'articles'

```

## 4. URL Parameters

### Path Converters

تستخدم لالتقاط أجزاء من URL وتحويلها إلى معاملات:

```python
urlpatterns = [
    path('articles/<int:id>/', views.article_detail),
    path('user/<str:username>/', views.user_profile),
    path('posts/<slug:slug>/', views.post_detail),
]
```

**أنواع Path Converters:**

- **ـ int:** أرقام صحيحة موجبة (0، 1، 2، ...)
- **ـ str:** أي نص (default) - لا يشمل الـ slash
- **ـ slug:** نص يحتوي على حروف، أرقام، شرطات، وunderscore
- **ـ uuid:** UUID format
- **ـ path:** أي نص يشمل الـ slash

### استخدام Parameters في Views

```python
def article_detail(request, id):
    article = Article.objects.get(id=id)
    return render(request, 'article.html', {'article': article})

def user_profile(request, username):
    user = User.objects.get(username=username)
    return render(request, 'profile.html', {'user': user})

```

### Multiple Parameters

```python
urlpatterns = [
    path('blog/<int:year>/<int:month>/<slug:slug>/', views.post_detail),
]

def post_detail(request, year, month, slug):
    post = Post.objects.get(year=year, month=month, slug=slug)
    return render(request, 'post.html', {'post': post})

```

### Query Parameters

تستخدم للحصول على معاملات من query string (بعد علامة ?):

```python
# URL: /search/?q=django&category=web
def search(request):
    query = request.GET.get('q', '')
    category = request.GET.get('category', '')
    results = search_items(query, category)
    return render(request, 'search.html', {'results': results})

```

### Optional Parameters

```python
from django.urls import path

urlpatterns = [
    path('articles/', views.article_list),
    path('articles/<int:year>/', views.article_list),
    path('articles/<int:year>/<int:month>/', views.article_list),
]

def article_list(request, year=None, month=None):
    if year and month:
        articles = Article.objects.filter(year=year, month=month)
    elif year:
        articles = Article.objects.filter(year=year)
    else:
        articles = Article.objects.all()
    return render(request, 'articles.html', {'articles': articles})

```

## 5. دوال مساعدة إضافية

### reverse()

تحويل اسم URL إلى المسار الفعلي:

```python
from django.urls import reverse

url = reverse('article_detail', kwargs={'id': 5})
# returns: '/articles/5/'
```

### redirect()

إعادة توجيه المستخدم إلى URL آخر:

```python
from django.shortcuts import redirect

def old_view(request):
    return redirect('new_view')

def login_required_view(request):
    if not request.user.is_authenticated:
        return redirect('login')

```

## 8. Best Practices

- استخدم أسماء واضحة للـ URLs مع معامل `name`
- استخدم `app_name` لتجنب تضارب الأسماء
- استخدم Class-Based Views للـ Views المعقدة
- اجعل URLs قابلة للقراءة (SEO-friendly)