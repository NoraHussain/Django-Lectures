# Model Basics

# Model Basics في Django

## ما هو الـ Model؟

الـ Model في Django هو class في Python يمثل جدول في قاعدة البيانات. كل attribute في الـ class يمثل field (حقل) في الجدول.

## إنشاء Model بسيط

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)
```

## أنواع الـ Fields

### 1ـ CharField

لحفظ نصوص قصيرة

- ـ `max_length`: (إجباري) الحد الأقصى لعدد الأحرف
- ـ `ـ blank`: (default: False) هل يمكن أن يكون فارغاً في forms
- ـ `null`: (default: False) هل يمكن أن يكون NULL في قاعدة البيانات
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False) هل يجب أن تكون القيمة فريدة
- ـ `db_index`: (default: False) إنشاء index على هذا الحقل
- ـ `verbose_name`: اسم وصفي للحقل
- ـ `help_text`: نص مساعد
- ـ `choices`: قائمة بالخيارات المتاحة
- ـ `editable`: (default: True) هل يمكن تعديله في forms
- ـ `validators`: قائمة من الـ validators
- ـ `error_messages`: رسائل خطأ مخصصة

### 2ـ TextField

لحفظ نصوص طويلة

- ـ `blank`: (default: False)
- ـ `ـnull`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `editable`: (default: True)
- ـ `validators`: قائمة من الـ validators

### 3ـ IntegerField

لحفظ أرقام صحيحة (من -2147483648 إلى 2147483647)

- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `choices`: قائمة بالخيارات
- ـ `validators`: قائمة من الـ validators

### 4ـ BigIntegerField

لحفظ أرقام صحيحة كبيرة (من -9223372036854775808 إلى 9223372036854775807)

- نفس خيارات IntegerField

### 5ـ SmallIntegerField

لحفظ أرقام صحيحة صغيرة (من -32768 إلى 32767)

- نفس خيارات IntegerField

### 6ـ PositiveIntegerField

لحفظ أرقام صحيحة موجبة فقط (من 0 إلى 2147483647)

- نفس خيارات IntegerField

### 7ـ PositiveSmallIntegerField

لحفظ أرقام صحيحة موجبة صغيرة (من 0 إلى 32767)

- نفس خيارات IntegerField

### 8ـ FloatField

لحفظ أرقام عشرية

- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `validators`: قائمة من الـ validators

### 9ـ DecimalField

لحفظ أرقام عشرية بدقة محددة (مناسب للأموال)

- ـ `max_digits`: (إجباري) إجمالي عدد الأرقام
- ـ `decimal_places`: (إجباري) عدد الأرقام بعد الفاصلة
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `validators`: قائمة من الـ validators

### 10ـ BooleanField

لحفظ قيم True/False

- ـ `default`: القيمة الافتراضية
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `editable`: (default: True)

### 11ـ DateField

لحفظ تاريخ (date)

- ـ `auto_now`: (default: False) يحدث التاريخ تلقائياً عند كل save
- ـ `auto_now_add`: (default: False) يضبط التاريخ تلقائياً عند الإنشاء فقط
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `validators`: قائمة من الـ validators

### 12ـ DateTimeField

لحفظ تاريخ ووقت

- ـ `auto_now`: (default: False)
- ـ `auto_now_add`: (default: False)
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد
- ـ `validators`: قائمة من الـ validators

### 13ـ TimeField

لحفظ وقت فقط

- ـ `auto_now`: (default: False)
- ـ `auto_now_add`: (default: False)
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد

### 14ـ DurationField

لحفظ فترة زمنية (timedelta)

- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد

### 15ـ EmailField

لحفظ عناوين البريد الإلكتروني (مع validation تلقائي)

- ـ `max_length`: (default: 254)
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد

### 16ـ URLField

لحفظ روابط URL (مع validation تلقائي)

- ـ `max_length`: (default: 200)
- ـ`blank`: (default: False)
- ـ`null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد

### 17ـ SlugField

لحفظ slugs (نصوص قصيرة تحتوي على أحرف، أرقام، شرطات وشرطات سفلية فقط)

- ـ `max_length`: (default: 50)
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `unique`: (default: False)
- ـ `db_index`: (default: True)
- ـ `allow_unicode`: (default: False) السماح بأحرف Unicode
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد

### 18ـ UUIDField

لحفظ UUID (Universally Unique Identifier)

- ـ `default`: القيمة الافتراضية (غالباً uuid.uuid4)
- ـ `unique`: (default: False)
- ـ `db_index`: (default: False)
- ـ `primary_key`: (default: False)
- ـ `editable`: (default: True)
- ـ `verbose_name`: اسم وصفي

### 19ـ FileField

لرفع وحفظ ملفات

- ـ `upload_to`: مسار حفظ الملف أو function
- ـ `max_length`: (default: 100) طول اسم الملف
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `storage`: نظام التخزين المستخدم
- ـ `verbose_name`: اسم وصفي
- ـ `help_text`: نص مساعد

### 20ـ ImageField

لرفع وحفظ صور (يتطلب مكتبة Pillow)

- ـ `upload_to`: مسار حفظ الصورة
- ـ `max_length`: (default: 100)
- ـ `height_field`: حقل لحفظ ارتفاع الصورة
- ـ `width_field`: حقل لحفظ عرض الصورة
- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `storage`: نظام التخزين
- ـ `verbose_name`: اسم وصفي

### 21ـ BinaryField

لحفظ بيانات binary خام

- ـ`blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية
- ـ `editable`: (default: False)
- ـ `verbose_name`: اسم وصفي

### 22ـ JSONField

لحفظ بيانات JSON

- ـ `blank`: (default: False)
- ـ `null`: (default: False)
- ـ `default`: القيمة الافتراضية (dict أو list)
- ـ `encoder`: JSON encoder مخصص
- ـ `decoder`: JSON decoder مخصص
- ـ `db_index`: (default: False)
- ـ `verbose_name`: اسم وصفي

## 23ـ Primary Key

كل model يجب أن يكون له primary key. Django تلقائياً تضيف:

```python
id = models.AutoField(primary_key=True)
```

يمكنك تحديد primary key مخصص:

```python
uuid = models.UUIDField(primary_key=True, default=uuid.uuid4)
```

## خيارات Meta

يمكن إضافة class Meta داخل الـ Model لتحديد إعدادات إضافية:

```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    
    class Meta:
        db_table = 'products'  # اسم الجدول في قاعدة البيانات
        ordering = ['-created_at']  # الترتيب الافتراضي
        verbose_name = 'Product'  # الاسم المفرد
        verbose_name_plural = 'Products'  # الاسم الجمع
        unique_together = [['field1', 'field2']]  # حقول يجب أن تكون فريدة معاً
        indexes = [models.Index(fields=['name'])]  # إضافة indexes
        constraints = [
		        models.UniqueConstraint(
		            fields=['field1', 'field2'],
		            name='unique_field1_field2'
		        ),
		        models.CheckConstraint(
		            check=models.Q(price__gte=0),
		            name='price_positive'
		        )
		    ]
    
    
```

## ـ Class Meta

### ـ db_table

اسم الجدول في قاعدة البيانات 

### ـ ordering

الترتيب الافتراضي للاستعلامات عند عمل 

```jsx
Product.objects.all()
```

### ـ verbose_name

اسم مفرد وصفي للـ Model يظهر في Django Admin

### ـ verbose_name_plural

اسم جمع وصفي للـ Model يظهر في Django Admin

### ـ unique_together

تحديد مجموعة حقول يجب أن تكون فريدة معاً (مهمل في Django 4.2+، استخدم UniqueConstraint بدلاً منه)

### ـ index_together

إنشاء index مركب على مجموعة حقول (مهمل في Django 4.2+، استخدم indexes بدلاً منه) 

### ـ indexes

قائمة بـ indexes المراد إضافتها على الجدول

```python
indexes = [
    models.Index(fields=['field1', 'field2']),
    models.Index(fields=['-created_at']),
]
```

### ـ constraints

قائمة بـ constraints المراد إضافتها

```python
constraints = [
    models.UniqueConstraint(fields=['field1', 'field2'], name='unique_fields'),
    models.CheckConstraint(check=models.Q(price__gte=0), name='price_positive'),
]
```

### ـ abstract

إذا كان True، يصبح الـ Model abstract base class (لا ينشئ جدول في قاعدة البيانات)

### ـ permissions

صلاحيات إضافية مخصصة للـ Model

```python
permissions = [
    ('can_publish', 'Can publish posts'),
    ('can_edit_published', 'Can edit published posts'),
]
```

### ـ default_permissions

الصلاحيات الافتراضية (default: ['add', 'change', 'delete', 'view'])

### ـ managed

إذا كان False، لن يدير Django migrations لهذا الجدول (default: True)

### ـ proxy

إذا كان True، يصبح الـ Model proxy model (يستخدم نفس جدول الـ Model الأصلي)

تغيير السلوك بدون إنشاء جدول جديد

## ـ Methods الشائعة في Models

### ـ __**str__**()

تحديد كيفية عرض الـ object كنص

```python
def __str__(self):
    return self.name
```

### ـ save()

يمكن override لإضافة logic مخصص قبل أو بعد الحفظ

```python
def save(self, *args, **kwargs):
    # Logic قبل الحفظ
    self.slug = slugify(self.name)
    super().save(*args, **kwargs)
    # Logic بعد الحفظ
```

### ـ delete()

يمكن override لإضافة logic مخصص قبل أو بعد الحذف

```python
def delete(self, *args, **kwargs):
    # Logic قبل الحذف
    super().delete(*args, **kwargs)
    # Logic بعد الحذف
```