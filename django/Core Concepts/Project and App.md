# Project and App

## ما هو Project و App في Django؟

### ـ Project

الـ **Project** في Django هو التطبيق الكامل أو الموقع بأكمله. يمثل المشروع الإطار الأساسي الذي يحتوي على جميع الإعدادات والتكوينات العامة.

**مكونات Project الأساسية:**

- ملف `settings.py` - ملف الإعدادات الذي يحتوي على configuration للمشروع بأكمله
- ملف `urls.py` - ملف routing الرئيسي الذي يوجه URLs للتطبيقات المختلفة
- ملفي `wsgi.py` و `asgi.py` - ملفات لنشر المشروع على servers
- ملف `manage.py` - أداة command-line للتفاعل مع المشروع

### ـ App

الـ **App** هو وحدة أو module مستقل داخل Project. كل app يؤدي وظيفة محددة ويمكن إعادة استخدامه في projects أخرى.

**مكونات App الأساسية:**

- ملف `models.py` - تعريف data models والجداول في database
- ملف `views.py` - كتابة business logic والتعامل مع requests
- ملف `urls.py` - routing خاص بهذا app
- ملف `templates/` - مجلد لحفظ HTML templates
- ملف `admin.py` - تسجيل models في Django admin panel

### العلاقة بينهما

يمكن أن يحتوي **Project** واحد على عدة **Apps**. مثلاً، موقع تجارة إلكترونية (Project) قد يحتوي على:

- ـ App للمستخدمين (users)
- ـ App للمنتجات (products)
- ـ App لسلة التسوق (cart)
- ـ App للمدفوعات (payments)

هذا التقسيم يجعل الكود منظمًا وسهل الصيانة، ويسمح بإعادة استخدام Apps في projects مختلفة.