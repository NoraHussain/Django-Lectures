# 1- ما هي Permissions في Django؟
ال **Permissions** هي آلية تحدد:
**من يستطيع أن يفعل ماذا داخل النظام**

مثل:
- من يستطيع رؤية البيانات
- من يستطيع الإضافة
- من يستطيع التعديل
- من يستطيع الحذف
- أو تنفيذ إجراء مخصص (Approve / Publish / Export…)


في Django تعتمد الصلاحيات على: **User** و **Group** و **Permission**

# 2- الصلاحيات الافتراضية (Built-in Permissions)

عند إنشاء أي **Model**، Django ينشئ تلقائياً 4 صلاحيات:

|Permission|الوصف|
|---|---|
|add_model|إضافة سجل|
|change_model|تعديل سجل|
|delete_model|حذف سجل|
|view_model|عرض السجلات|

`add_book`
`change_book`
`delete_book`
`view_book`


 اسم الصلاحية يكون:
 `<app_label>.<action>_<modelname>`

# 3-  أين تُخزّن Permissions؟

في جدول:

`django.contrib.auth.models.Permission`
ويتم الربط عبر - 
`user.user_permissions`
`group.permissions`

# 4- إنشاء صلاحيات مخصصة (Custom Permissions)

```
class Course(models.Model):
    title = models.CharField(max_length=200)

    class Meta:
        permissions = [
            ("publish_course", "Can publish course"),
            ("approve_course", "Can approve course"),
        ]
```

ثم 
```
python manage.py makemigrations
python manage.py migrate
```
الآن لديك 
```
app.publish_course
app.approve_course
```

# التحقق من الصلاحيات في الـ Views
##  الطريقة 1: `user.has_perm()`

```
def my_view(request):
	if not request.user.has_perm('courses.publish_course'):
		return HttpResponseForbidden("Access denied")
```

##  الطريقة 2: Decorator `@permission_required

```
from django.contrib.auth.decorators import permission_required

@permission_required('courses.publish_course',raise_exception=True)
def publish_course(request):
...
```

## الطريقة 3: Class Based Views

```
from django.contrib.auth.mixins import PermissionRequiredMixin

class CourseCreateView(PermissionRequiredMixin, CreateView):
    permission_required = 'courses.add_course'
```




# 7- ا Permissions داخل Templates

### فحص صلاحية:

```
{% if perms.courses.add_course %}
   <a href="{% url 'course_add' %}"> Add Course</a> 
{% endif %}
```





# 8- المستخدمين: Superuser و Staff

| النوع     | الصلاحيات             |
| --------- | --------------------- |
| Superuser | كل شيء بدون قيود      |
| Staff     | يستطيع دخول admin فقط |
| User عادي | حسب الصلاحيات         |
|           |                       |

`user.is_superuser user.is_staff`

**خطأ شائع:**  
`is_staff` ≠ يمتلك كل الصلاحيات