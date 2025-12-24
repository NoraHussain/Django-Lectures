# Relationships

# Relationships في Django

## ما هي الـ Relationships؟

الـ Relationships في Django هي طريقة لربط Models ببعضها البعض، تماماً كما تربط الجداول في قواعد البيانات العلائقية. Django توفر ثلاثة أنواع رئيسية من العلاقات.

## أنواع الـ Relationships

### 1ـ ForeignKey (Many-to-One)

علاقة واحد-إلى-كثير: كل record في جدول يمكن أن يرتبط بـ record واحد فقط في جدول آخر، لكن الـ record في الجدول الآخر يمكن أن يرتبط بعدة records.

مثال: كل مقال (Post) يكتبه مؤلف واحد (Author)، لكن المؤلف يمكن أن يكتب عدة مقالات.

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    
    def __str__(self):
        return self.name

class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    
    def __str__(self):
        return self.title
```

### ـ خيارات ForeignKey

- ـ `on_delete`: (إجباري) يحدد ماذا يحدث عند حذف الـ object المرتبط
    - ـ `models.CASCADE`: حذف جميع الـ objects المرتبطة
    - ـ `models.PROTECT`: منع الحذف إذا كانت هناك objects مرتبطة
    - ـ `models.SET_NULL`: تعيين القيمة إلى NULL (يتطلب null=True)
    - ـ `ـmodels.SET_DEFAULT`: تعيين القيمة إلى default
    - ـ `models.SET()`: تعيين القيمة إلى قيمة محددة أو نتيجة function
    - ـ `models.DO_NOTHING`: لا تفعل شيء (خطر: قد يسبب مشاكل في قاعدة البيانات)
- ـ `related_name`: الاسم المستخدم للوصول العكسي من الـ Model المرتبط

```python
class Post(models.Model):
    author = models.ForeignKey(
        Author, 
        on_delete=models.CASCADE,
        related_name='posts'  # الآن يمكن استخدام author.posts.all()
    )
```

- ـ `related_query_name`: الاسم المستخدم في الاستعلامات العكسية (default: نفس related_name)
- ـ `to_field`: الحقل المرتبط في الـ Model الآخر (default: primary key)
- ـ `db_constraint`: (default: True) إنشاء foreign key constraint في قاعدة البيانات
- ـ `limit_choices_to`: تحديد الخيارات المتاحة في forms

```python
author = models.ForeignKey(
    Author,
    on_delete=models.CASCADE,
    limit_choices_to={'is_active': True}
)
```

### ـ استخدام ForeignKey

```python
# إنشاء objects
author = Author.objects.create(name='محمد')
post = Post.objects.create(title='مقالة جديدة', author=author)

# الوصول من Post إلى Author
print(post.author.name)  # محمد

# الوصول العكسي من Author إلى Posts
posts = author.posts.all()  # إذا استخدمنا related_name='posts'
# أو
posts = author.post_set.all()  # الطريقة الافتراضية

# استعلامات
posts = Post.objects.filter(author__name='محمد')
authors = Author.objects.filter(posts__title__contains='جديدة')
```

### 2ـ OneToOneField

علاقة واحد-إلى-واحد: كل record في جدول يرتبط بـ record واحد فقط في جدول آخر والعكس صحيح.

مثال: كل مستخدم (User) له ملف شخصي واحد (Profile).

```python
from django.contrib.auth.models import User

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
    birth_date = models.DateField()
    
    def __str__(self):
        return f'Profile for {self.user.username}'
```

### ـ خيارات OneToOneField

ـ OneToOneField يقبل نفس خيارات ForeignKey:

- ـ `on_delete`: (إجباري)
- ـ `related_name`
- ـ `related_query_name`
- ـ `to_field`
- ـ `db_constraint`
- ـ `limit_choices_to`
- ـ `parent_link`: (default: False) يستخدم في Model inheritance

### ـ استخدام OneToOneField

```python
# إنشاء objects
user = User.objects.create_user('ahmed', 'ahmed@example.com')
profile = Profile.objects.create(
    user=user,
    bio='مطور Django',
    birth_date='1990-01-01'
)

# الوصول من Profile إلى User
print(profile.user.username)  # ahmed

# الوصول العكسي من User إلى Profile
print(user.profile.bio)  # مطور Django

# استعلامات
profiles = Profile.objects.filter(user__username='ahmed')
users = User.objects.filter(profile__bio__contains='Django')
```

### 3ـ ManyToManyField

علاقة كثير-إلى-كثير: كل record في جدول يمكن أن يرتبط بعدة records في جدول آخر والعكس صحيح.

مثال: كل طالب (Student) يمكن أن يسجل في عدة دورات (Course)، وكل دورة يمكن أن يسجل فيها عدة طلاب.

```python
class Student(models.Model):
    name = models.CharField(max_length=100)
    
    def __str__(self):
        return self.name

class Course(models.Model):
    name = models.CharField(max_length=100)
    students = models.ManyToManyField(Student, related_name='courses')
    
    def __str__(self):
        return self.name
```

### ـ خيارات ManyToManyField

- ـ `related_name`: الاسم المستخدم للوصول العكسي
- ـ `related_query_name`: الاسم المستخدم في الاستعلامات العكسية
- ـ `symmetrical`: (default: True) فقط لعلاقات من نفس Model، إذا كان True فالعلاقة متماثلة

```python
class Person(models.Model):
    friends = models.ManyToManyField('self', symmetrical=True)
    # إذا أضفت A كصديق لـ B، سيضاف B تلقائياً كصديق لـ A
```

- ـ `through`: تحديد Model وسيط مخصص للعلاقة
- ـ `through_fields`: تحديد الحقول المستخدمة في الـ Model الوسيط
- ـ `db_table`: اسم جدول العلاقة في قاعدة البيانات
- ـ `db_constraint`: (default: True)
- ـ `limit_choices_to`: تحديد الخيارات المتاحة

### ـ استخدام ManyToManyField

```python
# إنشاء objects
student1 = Student.objects.create(name='علي')
student2 = Student.objects.create(name='فاطمة')
course = Course.objects.create(name='Python Programming')

# إضافة طلاب للدورة
course.students.add(student1, student2)
# أو
course.students.add(student1)
course.students.add(student2)

# إضافة دورات للطالب (الوصول العكسي)
student1.courses.add(course)

# الحصول على جميع طلاب الدورة
students = course.students.all()

# الحصول على جميع دورات الطالب
courses = student1.courses.all()

# إزالة علاقة
course.students.remove(student1)

# إزالة جميع العلاقات
course.students.clear()

# استبدال جميع العلاقات
course.students.set([student1, student2])

# استعلامات
courses = Course.objects.filter(students__name='علي')
students = Student.objects.filter(courses__name__contains='Python')
```

### ـ ManyToManyField مع Through Model

عندما تحتاج إلى تخزين معلومات إضافية عن العلاقة، يمكنك استخدام through model.

مثال: تسجيل الطالب في دورة مع تاريخ التسجيل والدرجة.

```python
class Student(models.Model):
    name = models.CharField(max_length=100)
    
    def __str__(self):
        return self.name

class Course(models.Model):
    name = models.CharField(max_length=100)
    students = models.ManyToManyField(
        Student,
        through='Enrollment',
        related_name='courses'
    )
    
    def __str__(self):
        return self.name

class Enrollment(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    date_enrolled = models.DateField(auto_now_add=True)
    grade = models.CharField(max_length=2, blank=True)
    
    class Meta:
        unique_together = ['student', 'course']
    
    def __str__(self):
        return f'{self.student.name} enrolled in {self.course.name}'
```

### ـ استخدام Through Model

```python
# إنشاء objects
student = Student.objects.create(name='أحمد')
course = Course.objects.create(name='Django Web Development')

# لا يمكن استخدام add() مباشرة مع through model
# يجب إنشاء object من Enrollment
enrollment = Enrollment.objects.create(
    student=student,
    course=course,
    grade='A'
)

# الحصول على جميع تسجيلات الطالب
enrollments = student.enrollment_set.all()

# الحصول على دورات الطالب مع معلومات التسجيل
for enrollment in student.enrollment_set.all():
    print(f'{enrollment.course.name}: {enrollment.grade}')

# استعلامات متقدمة
# الحصول على جميع الطلاب الحاصلين على A في أي دورة
students_with_A = Student.objects.filter(enrollment__grade='A').distinct()

# الحصول على جميع الدورات التي سجل فيها الطالب بعد تاريخ معين
from datetime import date
courses = Course.objects.filter(
    enrollment__student=student,
    enrollment__date_enrolled__gte=date(2024, 1, 1)
)
```

## ـ Self-referential Relationships

علاقة الـ Model بنفسه، مفيدة في حالات مثل التعليقات المتداخلة أو الهيكل التنظيمي.

### ـ ForeignKey إلى نفس الـ Model

```python
class Comment(models.Model):
    text = models.TextField()
    parent = models.ForeignKey(
        'self',
        on_delete=models.CASCADE,
        null=True,
        blank=True,
        related_name='replies'
    )
    
    def __str__(self):
        return self.text[:50]
```

```python
# الاستخدام
main_comment = Comment.objects.create(text='تعليق رئيسي')
reply1 = Comment.objects.create(text='رد 1', parent=main_comment)
reply2 = Comment.objects.create(text='رد 2', parent=main_comment)

# الحصول على جميع الردود
replies = main_comment.replies.all()
```

### ـ ManyToManyField إلى نفس الـ Model

```python
class Person(models.Model):
    name = models.CharField(max_length=100)
    friends = models.ManyToManyField('self', blank=True)
    
    def __str__(self):
        return self.name
```

```python
# الاستخدام
person1 = Person.objects.create(name='أحمد')
person2 = Person.objects.create(name='محمد')

person1.friends.add(person2)
# إذا كان symmetrical=True (الافتراضي)، سيضاف person1 تلقائياً لأصدقاء person2
```

### ـ ManyToManyField غير متماثل

```python
class User(models.Model):
    username = models.CharField(max_length=100)
    following = models.ManyToManyField(
        'self',
        symmetrical=False,
        related_name='followers',
        blank=True
    )
    
    def __str__(self):
        return self.username
```

```python
# الاستخدام
user1 = User.objects.create(username='ahmed')
user2 = User.objects.create(username='mohamed')

# user1 يتابع user2
user1.following.add(user2)

# الحصول على من يتابعهم user1
following = user1.following.all()

# الحصول على متابعي user2
followers = user2.followers.all()
```

## ـ Generic Relations

تسمح بإنشاء علاقات مع أي Model دون تحديد Model معين مسبقاً. تستخدم ContentTypes framework.

```python
from django.contrib.contenttypes.fields import GenericForeignKey, GenericRelation
from django.contrib.contenttypes.models import ContentType

class Comment(models.Model):
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey('content_type', 'object_id')
    text = models.TextField()
    
    def __str__(self):
        return self.text[:50]

class Post(models.Model):
    title = models.CharField(max_length=200)
    comments = GenericRelation(Comment)
    
    def __str__(self):
        return self.title

class Photo(models.Model):
    title = models.CharField(max_length=200)
    comments = GenericRelation(Comment)
    
    def __str__(self):
        return self.title
```

```python
# الاستخدام
post = Post.objects.create(title='مقالة جديدة')
photo = Photo.objects.create(title='صورة جميلة')

# إضافة تعليقات
Comment.objects.create(content_object=post, text='تعليق على المقالة')
Comment.objects.create(content_object=photo, text='تعليق على الصورة')

# الوصول العكسي
post_comments = post.comments.all()
photo_comments = photo.comments.all()
```

## ـ Best Practices للـ Relationships

### ـ استخدام related_name بوضوح

دائماً حدد related_name واضح لتجنب التضارب والارتباك.

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Post(models.Model):
    author = models.ForeignKey(
        Author,
        on_delete=models.CASCADE,
        related_name='posts'  # واضح وسهل الفهم
    )
```

### ـ اختيار on_delete المناسب

فكر جيداً في السلوك المطلوب عند الحذف.

- `CASCADE`: للعلاقات التابعة (مثل Post ينتمي لـ Author)
- `PROTECT`: لمنع الحذف غير المقصود (مثل Category لا يحذف إذا كان له Products)
- `SET_NULL`: للعلاقات الاختيارية (مثل optional Author)

### ـ استخدام select_related و prefetch_related

لتحسين الأداء عند الاستعلام عن objects مع علاقاتها.

```python
# select_related للـ ForeignKey و OneToOneField
posts = Post.objects.select_related('author').all()

# prefetch_related للـ ManyToManyField والعلاقات العكسية
authors = Author.objects.prefetch_related('posts').all()

# استخدام متعدد
courses = Course.objects.select_related('department').prefetch_related('students').all()
```

### ـ تجنب Circular Imports

استخدم string reference عند الإشارة لـ Model في نفس الملف أو لتجنب circular imports.

```python
# بدلاً من
from myapp.models import OtherModel
author = models.ForeignKey(OtherModel, on_delete=models.CASCADE)

# استخدم
author = models.ForeignKey('myapp.OtherModel', on_delete=models.CASCADE)
# أو للـ Model في نفس الملف
author = models.ForeignKey('Author', on_delete=models.CASCADE)
```

### ـ استخدام unique_together أو UniqueConstraint

لضمان عدم تكرار العلاقات في ManyToMany مع through model.

```python
class Enrollment(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    
    class Meta:
        constraints = [
            models.UniqueConstraint(
                fields=['student', 'course'],
                name='unique_student_course'
            )
        ]
```

## ـ استعلامات متقدمة مع Relationships

### ـ Filtering عبر Relationships

```python
# الحصول على Authors الذين كتبوا posts تحتوي على كلمة معينة
authors = Author.objects.filter(posts__title__contains='Django')

# استخدام __ للوصول لعلاقات متعددة
comments = Comment.objects.filter(post__author__name='أحمد')

# استخدام Q objects لاستعلامات معقدة
from django.db.models import Q
posts = Post.objects.filter(
    Q(author__name='أحمد') | Q(author__name='محمد')
)
```

### ـ Aggregation عبر Relationships

```python
from django.db.models import Count, Avg

# عدد Posts لكل Author
authors = Author.objects.annotate(post_count=Count('posts'))

# متوسط عدد Students لكل Course
courses = Course.objects.annotate(student_count=Count('students'))

# Authors الذين كتبوا أكثر من 5 posts
prolific_authors = Author.objects.annotate(
    post_count=Count('posts')
).filter(post_count__gt=5)
```

### ـ Exists للتحقق من وجود علاقات

```python
# التحقق إذا كان للـ author posts
has_posts = author.posts.exists()

# الحصول على Authors الذين لديهم posts
authors_with_posts = Author.objects.filter(posts__isnull=False).distinct()
# أو
from django.db.models import Exists, OuterRef
authors_with_posts = Author.objects.filter(
    Exists(Post.objects.filter(author=OuterRef('pk')))
)
```

## ـ Performance Considerations

### ـ N+1 Query Problem

تجنب المشكلة الشائعة حيث يتم تنفيذ استعلام منفصل لكل object.

```python
# سيء: N+1 queries
posts = Post.objects.all()
for post in posts:
    print(post.author.name)  # استعلام منفصل لكل post

# جيد: استعلام واحد
posts = Post.objects.select_related('author').all()
for post in posts:
    print(post.author.name)  # لا استعلامات إضافية
```

### ـ استخدام only() و defer()

لتحميل حقول محددة فقط.

```python
# تحميل حقول محددة فقط
posts = Post.objects.only('title', 'author__name')

# تأجيل تحميل حقول معينة
posts = Post.objects.defer('content')
```

### ـ Bulk Operations

لإضافة أو حذف علاقات متعددة بكفاءة.

```python
# إضافة عدة students دفعة واحدة
course.students.add(student1, student2, student3)

# أو باستخدام list
students = [student1, student2, student3]
course.students.add(*students)

# Bulk create لـ through model
Enrollment.objects.bulk_create([
    Enrollment(student=student1, course=course),
    Enrollment(student=student2, course=course),
    Enrollment(student=student3, course=course),
])
```