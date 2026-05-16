У тебя тут не “одна ошибка”, а смешано несколько критических проблем, из-за которых регистрация через OAuth (GitHub/Google) просто не может работать нормально.

Я разберу по факту.

🚨 1. Самая большая проблема: кастомный User НЕ настроен полностью

Ты сделал:

class UserProfile(AbstractUser):

И в settings:

AUTH_USER_MODEL = 'store_app.UserProfile'

✔ это правильно

НО дальше проблема:

❌ Ты НЕ создал корректную миграцию с нуля

Если ты добавил кастомного пользователя ПОСЛЕ миграций — Django ломается.

Проверка:

Если ты уже запускал migrate до этого — это почти всегда причина “регистрация не работает”.

🚨 2. ImageField без upload_to (ломает создание пользователя)
profile_image = models.ImageField()

❌ Это может падать при регистрации через allauth, потому что он пытается создать user без файла.

✔ исправь:
profile_image = models.ImageField(
    upload_to='profiles/',
    blank=True,
    null=True
)
🚨 3. Главная причина твоего OAuth “не работает”

Ты используешь:

GitHub OAuth
Google OAuth
Django Allauth

НО у тебя нет настройки Sites + SocialApp нормально связанной

🔥 ОБЯЗАТЕЛЬНО проверь:
1. Sites

Админка:

/admin/sites/site/

Должно быть:

Domain: 127.0.0.1:8000
2. SocialApp
/admin/socialaccount/socialapp/

Там:

GitHub
Google
Sites → ОБЯЗАТЕЛЬНО выбран 127.0.0.1:8000
🚨 4. Ты используешь i18n_patterns (очень важно)

У тебя:

urlpatterns = i18n_patterns(
    path('accounts/', include('allauth.urls')),
)
⚠️ Это ломает OAuth redirect

Потому что Django теперь делает:

/ru/accounts/github/login/callback/

НО в GitHub ты указал:

/accounts/github/login/callback/

❌ mismatch = OAuth FAIL

✔ РЕШЕНИЕ (самое важное)
В GitHub и Google добавь:
http://127.0.0.1:8000/ru/accounts/github/login/callback/
http://127.0.0.1:8000/ru/accounts/google/login/callback/

Или убери i18n_patterns.

🚨 5. Ты НЕ показал allauth settings

Добавь обязательно:

ACCOUNT_EMAIL_REQUIRED = True
ACCOUNT_USERNAME_REQUIRED = True
ACCOUNT_AUTHENTICATION_METHOD = 'username_email'
LOGIN_REDIRECT_URL = '/'
🚨 6. Ты можешь вообще НЕ создавать user вручную

Allauth должен сам создавать UserProfile, но только если:

✔ SocialApp правильно подключён
✔ Sites правильно выбран
✔ redirect URI совпадает
✔ нет i18n конфликтов

💥 Почему у тебя “регистрация не работает” (итог)

У тебя одновременно:

❌ mismatch redirect_uri (/ru/ vs /)
❌ ImageField без настроек
❌ i18n_patterns ломает callback
❌ SocialApp не гарантированно привязан к Sites
❌ возможные старые миграции User
✅ Быстрое решение (самый короткий путь)

Сделай 4 шага:

1. Исправь ImageField
upload_to='profiles/',
blank=True,
null=True
2. Добавь /ru/ callback в OAuth (если i18n используешь)
3. Проверь Sites = 127.0.0.1:8000
4. Пересоздай миграции (если проект не в проде)
rm db.sqlite3
python manage.py makemigrations
python manage.py migrate
👍 Если хочешь

Я могу тебе:

собрать полностью рабочий Django + Allauth + GitHub + Google шаблон
или 
найти ТОЧНО почему у тебя сейчас падает login (по логам дальше)

Просто скажи 👍
