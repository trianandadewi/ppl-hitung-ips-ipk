# Membuat Django project myproject
# --------------------------------
`django-admin startproject myproject ./`

# Membuat Django app myapp
# ------------------------
`cd ./myproject`
`python manage.py startapp myapp`

# Memulai server
# --------------
`python manage.py runserver 80`

# Ganti Default Port
# ------------------
> manage.py
```
    # Override default port for `runserver` command
    from django.core.management.commands.runserver import Command as runserver
    runserver.default_port = "80"
```

# Menginstall myapp ke myproject
# ------------------------------
myproject > settings.py:
```
    INSTALLED_APPS = [
        'myapp',
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]
```

# Menambahkan route myapp ke myproject
# ------------------------------------
myproject > urls.py
```
    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path('', include('myapp.urls')),
        path('admin/', admin.site.urls),
    ]
```
# Membuat urls.py di myapp
# ------------------------
myapp > urls.py

# Membuat route '' myapp
# ----------------------
myapp > urls.py
```
    from django.urls import path
    from . import views

    urlpatterns = [
        path("", views.index, name="index")
    ]
```

# Membuat views sederhana
# -----------------------
myapp > views.py
```
    def index(request):
        return render(request, "myapp/index.html")
```

# Membuat folder myapp > templates > myapp
# ----------------------------------------

# Membuat index.html
# ------------------
myapp > templates > myapp > index.html
```
    <!DOCTYPE html>
    <html lang="en">
        <head>
            <title>Hello</title>
        </head>
        <body>
            <h1>Hello, World!</h1>
        </body>
    </html>
```

# Setting Allowed Host & CSRF
# ------------------
myproject > settings.py
```
ALLOWED_HOSTS = ['127.0.0.1', 'localhost', 'app.210555.xyz']
CSRF_TRUSTED_ORIGINS = ['http://app.210555.xyz']
```

# Setting STATIC
# ------------------
```
# Url at which static files are served
# It's the url the browser will fetch to get the static files
# It's prepend to static name by the {% static %} templatetag
STATIC_URL = "/static/"

# Directory where static files can be found
# When DEBUG = True, static files will be directly served from there by
# the manage.py runserver command
STATICFILES_DIRS = [BASE_DIR / "static"]

# Directory to export staticfiles for production
# All files from all STATICFILES_DIRS will be copied by
# manage.py collectstatic to this directory.
# /!\ It will not be served by django, you have to setup
# your webserver (or use a third party module)
# to serve assets from there.
STATIC_ROOT = BASE_DIR / "assets"
```

REGEX SALAHHHHH
----------------
```
find = <(link|img|script)([^>]*)(href|src)=["']([^"']*)["']
replace = <$1$2 $3="{% static myapp/'$4' %}"
replace bener = <$1$2 $3="{% static 'myapp/$4' %}"
```

REGEX BENER
-----------
```
find = <(link|img|script)([^>]*)(src)=["'](?!https?:\/\/)([^"']*)["']
replace = <$1$2 $3="{% static 'myapp/$4' %}"
```

# Collect Static PROD (DEBUG = False)
-------------------------------------
python manage.py collectstatic

Location static server
----------------------
```
  location /static {
    alias /home/coba/htdocs/coba.210555.xyz/web-django/assets/;
    add_header Cache-Control "public";
    autoindex off;  # Nonaktifkan directory listing
  }
```

HAPUS LINE INI (GAPERLU location ~*, hapus aja)
-----------------------------------------------
```
  location ~* ^.+\.(css|js|jpg|jpeg|gif|png|ico|gz|svg|svgz|ttf|otf|woff|woff2|eot|mp4|ogg|ogv|webm|webp|zip|swf)$ {
    add_header Access-Control-Allow-Origin "*";
    expires max;
    access_log on;
  }
```

# RUN DI GCP COMPUTE ENGINE
```
sudo "/home/gcp/.env/bin/python3" manage.py runserver 0.0.0.0:80
```

ERROR
-----
error 404 = artinya lupa masukin domain ke allowed host (settings.py)
error 403 = vhost belum dibuka
error 404 = vhost belum setting location /static
error CSRF = tambah ini di bawah allowed_hosts (settings.py):
CSRF_TRUSTED_ORIGINS = ['app.210555.xyz']

CERTIFICATE SSL
---------------
/etc/nginx/ssl-certificates/

`sudo cp /etc/nginx/ssl-certificates/coba.210555.xyz.key ~/coba.210555.xyz.key`
`sudo cp /etc/nginx/ssl-certificates/coba.210555.xyz.crt ~/coba.210555.xyz.crt`
`sudo cp /etc/nginx/ssl-certificates/coba.210555.xyz.key /home/coba/coba.210555.xyz.key`
`sudo cp /etc/nginx/ssl-certificates/coba.210555.xyz.crt /home/coba/coba.210555.xyz.crt`

# UWSGI
# -----
https://www.youtube.com/watch?v=ZpR1W-NWnp4&t=1485s