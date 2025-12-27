**كل ما يخص `media` في Django** بشكل منظم من الأساس إلى الاستخدام العملي، مع توضيح الفرق بينها وبين `static` لأن هذا لبس شائع.

---

## ما المقصود بـ Media في Django؟

ملفات **Media** هي **الملفات التي يرفعها المستخدمون** أثناء استخدام الموقع.

أمثلة:
- صورة كتاب
- صورة مستخدم (Profile Picture)
- ملف PDF
- فيديو
- أي ملف يتم رفعه من خلال نموذج (Form)

 القاعدة:
> **أي ملف مصدره المستخدم = Media**

---

## الفرق بين Media و Static

|         Static          |         Media          |
| :---------------------: | :--------------------: |
| ملفات ثابتة من المطوّر  |   ملفات من المستخدم    |
| CSS – JS – Images ثابتة | Images – PDFs – Videos |
|     لا تتغير غالبًا     |     تتغير باستمرار     |
|   تُحفظ داخل المشروع    |    تُحفظ خارج الكود    |

---

##  إعداد Media في `settings.py`

لاستخدام media يجب تعريف متغيرين أساسيين:

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

### ماذا تعني؟
- ا **MEDIA_URL**  
	الرابط الذي سيُستخدم في المتصفح.

- ا **MEDIA_ROOT**  
    المسار الفعلي على القرص حيث تُحفظ الملفات

النتيجة:

```
project/
│
├── media/
│   ├── books/
│   ├── profiles/
│   └── uploads/
```

---

##  ربط Media مع `urls.py`

في وضع التطوير فقط ==(DEBUG=True):==

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # urls
]

if settings.DEBUG:
    urlpatterns += static(
        settings.MEDIA_URL,
        document_root=settings.MEDIA_ROOT
    )
```

بدون هذا الربط:
- الملفات تُرفع.
- لكن لا تُعرض في المتصفح.

---

##  استخدام Media داخل Models

لاستخدام media نحتاج:

- ا`ImageField` للصور
- ا`FileField` للملفات العامة
### مثال:

```python
class Book(models.Model):
    title = models.CharField(max_length=200)
    cover = models.ImageField(upload_to='books/')
```

 ا `upload_to`: يحدد المجلد داخل `media`
- النتيجة:

```
media/books/cover.jpg
```

ملاحظة مهمة:
حقل ImageField يحتاج تثبيت:

```bash
pip install pillow
```

---

## رفع الملفات من Form

لكي يعمل رفع الملفات:
1. يجب أن يحتوي `<form>` على:

```html
<form method="post" enctype="multipart/form-data">
```

2. في الـ View:

```python
if request.method == 'POST':
    form = BookForm(request.POST, request.FILES)
```

بدون `request.FILES` → الملف لن يُحفظ

---

## عرض ملفات Media في Templates

لعرض صورة أو ملف:
```html
<img src="{{ book.cover.url }}">
```

أو رابط تحميل:

```html
<a href="{{ book.file.url }}">Download</a>
```

الخصائص المتاحة:

- `.url` → رابط العرض
- `.path` → المسار على السيرفر
- `.name` → اسم الملف

---

## ا Media داخل Django Admin

ا Admin يدعم Media تلقائيًا.

مثال:

```python
@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'cover')
```

وسيظهر:
- زر رفع ملف
- معاينة الصورة

---

## حذف ملفات Media

ا Django **لا يحذف الملف تلقائيًا** عند حذف السجل.

### الحل الشائع:

استخدام signal:

```python
from django.db.models.signals import post_delete
from django.dispatch import receiver

@receiver(post_delete, sender=Book)
def delete_cover(sender, instance, **kwargs):
    if instance.cover:
        instance.cover.delete(save=False)
```

---
## تغيير مكان التخزين (متقدم)

يمكن:
- التخزين على Amazon S3,  Google Cloud,  FTP أو أي Storage مخصص
باستخدام:

```python
DEFAULT_FILE_STORAGE
```

لكن هذا مستوى متقدم ويُستخدم غالبًا في Production.

---

## ا Media في Production (مهم جدًا)

في production :
-  لا تُستخدم `static()`
- السيرفر (Nginx / Apache) هو من يخدم media
- ا Django فقط يُخزن المسارات
