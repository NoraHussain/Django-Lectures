
#  الفكرة العامة عن Class-Based Views (CBV)

كل View في Django هو **كلاس** فيه:
- دوال جاهزة (Hooks)
- كل دالة لها **توقيت** في دورة الطلب (Request Lifecycle)

 أنت لا تكتب كل شيء من الصفر  
 أنت **تتدخل فقط حيث تحتاج**

# دورة الطلب (Lifecycle) – مبسطة

```
URL
 ↓
View
 ↓
get_queryset / get_object
 ↓
get_context_data
 ↓
template
 ↓
response
```

---

# ا ListView (عرض قائمة)

### الهدف

عرض مجموعة من الكائنات (Objects)

---

## أهم الخصائص (Attributes)

```python
model = Book
template_name = "books.html"
context_object_name = "books"
paginate_by = 10
ordering = ["-created_at"]
```

---

## أهم الدوال

### 1 ا get_queryset()

**أهم دالة في ListView**

 مسؤولة عن:
 ما هي البيانات التي ستُعرض؟

```python
def get_queryset(self):
    return Book.objects.filter(is_active=True)
```

مع Guardian:

```python
get_objects_for_user(...)
```


### 2ا get_context_data()

إضافة متغيرات إضافية للـ template

```python
def get_context_data(self, **kwargs):
    context = super().get_context_data(**kwargs)
    context["title"] = "Books"
    return context
```


### 3ا  paginate_queryset()

التحكم في الـ pagination


### 4ا get_ordering()

تحديد الترتيب ديناميكيًا

---

#  عرض عنصر واحد DetailView

### الهدف

عرض كائن واحد فقط


## أهم الخصائص

```python
model = Book
pk_url_kwarg = "id"
context_object_name = "book"
```

---

## أهم الدوال

### 1ا get_object()

**أهم دالة في DetailView**

مسؤولة عن:
أي كائن سيتم عرضه؟

```python
def get_object(self):
    obj = super().get_object()
    return obj
```

مع صلاحيات:

```python
if not self.request.user.has_perm("view_book", obj):
    raise PermissionDenied
```

### 2ا  get_queryset()

يُستخدم أحيانًا بدل `get_object`



### 3ا get_context_data()

إرسال بيانات إضافية للـ template


---

# إضافة عنصر CreateView
### الهدف

إنشاء كائن جديد


## أهم الخصائص

```python
model = Book
fields = ["title", "author"]
success_url = reverse_lazy("books")
```

## أهم الدوال

### 1ا  form_valid(form) ⭐⭐⭐

يُنفّذ بعد نجاح التحقق من الفورم

المكان المثالي لـ:
- حفظ بيانات إضافية
- إسناد الصلاحيات assign_perm
- ربط المستخدم بالكائن

```python
def form_valid(self, form):
    form.instance.created_by = self.request.user
    return super().form_valid(form)
```


### 2ا  form_invalid(form)

لو الفورم فيه أخطاء



### 3ا get_form()

التحكم في الفورم نفسه



### 4ا get_success_url()

تحديد وجهة التحويل بعد الحفظ

---

# تعديل عنصر UpdateView

### الهدف

تعديل كائن موجود


## أهم الدوال

### 1ا  get_object() 

جلب الكائن المراد تعديله
هنا نتحقق من الصلاحيات

```python
if not self.request.user.has_perm("change_book", obj):
    raise PermissionDenied
```


### 1ا form_valid()

بعد نجاح التعديل

### 2ا get_queryset()

للتحكم في أي عناصر يمكن تعديلها

---

# حذف عنصر DeleteView 
### الهدف

حذف كائن

## أهم الخصائص

```python
model = Book
success_url = reverse_lazy("books")
```

## أهم الدوال

### 1ا get_object() ⭐⭐⭐

جلب الكائن المراد حذفه
أهم نقطة أمان


### 1ا delete()

تنفيذ الحذف فعليًا

```python
def delete(self, request, *args, **kwargs):
    return super().delete(request, *args, **kwargs)
```

---

# دوال مشتركة بين كل الـ CBVs

### 1ا  dispatch() ⭐⭐⭐

أول دالة تُنفّذ
مثالية للتحقق من:
- تسجيل الدخول
- الأدوار role
- الصلاحيات- permissions عامة


### 2ا get()
لـ GET request



### 3ا  post()
لـ POST request

### 4ا setup()
تهيئة الكلاس



#  كيف تختار الدالة الصحيحة؟

| تريد                 | استخدم           |
| -------------------- | ---------------- |
| فلترة البيانات       | get_queryset     |
| حماية عنصر           | get_object       |
| تنفيذ شيء بعد الحفظ  | form_valid       |
| إضافة بيانات للواجهة | get_context_data |
| تحقق عام             | dispatch         |



# ربطها مع Guardian (ملخص)

| الحالة             | المكان       |     |
| ------------------ | ------------ | --- |
| عرض عناصر مصرح بها | get_queryset |     |
| حماية عنصر         | get_object   |     |
| إعطاء صلاحيات      | form_valid   |     |
| حماية كل الكلاس    | dispatch     |     |
