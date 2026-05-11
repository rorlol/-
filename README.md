1. pip install django
2. pip install djangorestframework
3. django-admin startproject mysite
4. cd mysite
5. python manage.py startapp store_app
6. settings.py
    .env
    pip install python-dotenv
    .gitignore -> .env
7. models.py
8. python manage.py makemigration & migrate
9. admin.py -> register(models.py -classes)
10. python manage.py createsuperuser
11. serializers.py -> json
12. views.py -> viewset.ModelViewSet -> CRUD

Client -> get -> Read(получить данные/маалымат алуу)
Client -> post -> Create(создавать)
Client -> put/patch -> Update(изменить, озгортуу)
Client -> delete -> Delete(удалить, очуруу)

13. urls.py ->  mysite/urls.py -> (store_app.urls), router
14. modeltranslation(en, ru, ky)

14.1, pip install django-modeltranslation

14.2. settings.py -> INSTALLED_APP  —>  'modeltranslation'

14.3. settings.py -> USE_L10N = True

14.4. settings.py ->
LANGUAGES = (
    ('ky', 'Kyrgyz')
)

14.5. MODELTRANSLATION_DEFAULT_LANGUAGE = 'ky'

14.6. MODELTRANSLATION_LANGUAGES = ('ky')

14.7. settings.py -> middlware ->
...
...
'django.middleware.locale.LocaleMiddleware',
...

14.8. mysite/app -> (store_app) -> translation.py

from .models import Product, Category, SubCategory
from modeltranslation.translator import TranslationOptions,register

@register(Product)
class ProductTranslationOptions(TranslationOptions):
    fields = ('product_name', 'description')

14.9. admin.py -> for comfortable:

from modeltranslation.admin import TranslationAdmin
@admin.register(Product)
class AllAdmin(TranslationAdmin):

    class Media:
        js = (
            'http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js',
            'http://ajax.googleapis.com/ajax/libs/jqueryui/1.10.2/jquery-ui.min.js',
            'modeltranslation/js/tabbed_translation_fields.js',
        )
        css = {
                'screen': ('modeltranslation/css/tabbed_translation_fields.css',),
        }

14.10
from django.conf.urls.i18n import i18n_patterns
# example
urlpatterns = i18n_patterns(
    path('admin/', admin.site.urls),
)

15. расширение -> admin.py(inline)
