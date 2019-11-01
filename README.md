# django_channels


## Configuration setting

```
    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django.contrib.sites',
    
    'channels',

]



  ROOT_URLCONF = 'nan_blog.urls'
  ASGI_APPLICATION = "nan_blog.routing.application"
```

## conf projet/consumers.py

```
    import  asyncio
    import json
    from django.contrib.auth import get_user_model
    from channels.consumer import asyncConsumer
    from channels.db import database_sync_to_async
    from .models import Thread, ChatMessage

    class ChatConsumer(asyncConsumer):
        async def websocket_connect(self,event):
            print('connected', event)

        async def websocket_receive(self,event):
            print('receive', event)

        async def websocket_disconnect(self,event):
            print('disconnected', event)

```


## conf projet/asgi.py

```
    import os
    import django
    from channels.routing import get_default_application

    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "nan_blog.settings")
    django.setup()
    application = get_default_application()

```

## conf projet/routing.py

```
    from django.conf.url import url
    from channels.routing import ProtocolTypeRouter, URLRouter
    from channels.auth import authMiddlewareStack
    from channels.security.websocket import AllowedHostsOriginValidator, OriginValidator

    from chat.consumer import ChatConsumer
    application = ProtocolTypeRouter({
        # Empty for now (http->django views is added by default)
        'websocket':AllowedHostsOriginValidator(
            authMiddlewareStack(
                URLRouter(
                    [
                        url(r'^(?P<username>[\w.@+-]+)', ChatConsumer),

                    ]
                )
            )
        )
    })

```

## conf projet/urls.py

```
    from django.contrib import admin
from django.urls import path, re_path
from . import views
from .views import ThreadView, InboxView

app_name = 'blog'
urlpatterns = [

    path('inbox', InboxView.as_view()),
    re_path(r'^(?P<username>[\w.@+-]+)', ThreadView.as_view()),

]
  
```
