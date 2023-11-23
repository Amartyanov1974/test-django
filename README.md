# Django site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри конейнера Django запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).

## Подготовительные работы

Заходим в директорию с Dockerfile (backend_main_django) и создаем образ
```
docker build -t local-image:tagname .
```
Создаем на [dockerhub](https://hub.docker.com/) репозиторий.<br>
![Снимок экрана_2023-11-23_21-26-46](https://github.com/Amartyanov1974/test-django/assets/74543172/ed3db12a-830c-477f-a7f9-f634e23f80eb)

Переименовываем образ:<br>
```
docker tag local-image:tagname new-repo:tagname
```

Авторизуемся в терминале:
```
docker login
```
При первой авторизации будет запрос логина и пароля.<br>
![Снимок экрана_2023-11-23_21-16-17](https://github.com/Amartyanov1974/test-django/assets/74543172/bc556082-ab56-471f-ba8f-95cb339bebc7)

Запушим образ в репозиторий<br>
```
docker push new-repo:tagname
```



## Установка
Переходим в директорию deploy и разворачиваем проект:

```
kubectl apply -f ./
```

Заходим по [ссылке](https://edu-reverent-mestorf.sirius-k8s.dvmn.org/admin)
![Снимок экрана_2023-11-23_21-23-15](https://github.com/Amartyanov1974/test-django/assets/74543172/fc59a7da-6e06-4e8a-a58f-f356ff1fb6e3)

## Ссылка на описание выделенных ресурсов облачной инфраструктуры
[Ссылка](https://sirius-env-registry.website.yandexcloud.net/edu-reverent-mestorf.html)

## Пояснения к файлам манифестов

djangoapp-configmap.yaml - содержат переменные виртуального окружения.<br>
djangoapp-deploy.yaml- для развертывания реплик контейнерных приложений.<br>
djangoapp-service.yaml - описывают способ доступа к приложению внутри кластера и маршрутизации трафика.<br>
djangoapp-ingress.yaml - описывает запросы к сетевым ресурсам.<br>
django-clearsessions.yaml - создание cron для очистки сессии. По умолчанию, для проверки работы стоит запуск 1 раз в 5 минут.<br>
django-migrate.yaml - разовая задача для migrate.<br>


## Переменные окружения

Переменные для образа postgresql:

`POSTGRES_DB` -- имя базы данных.

`POSTGRES_USER` -- пользователь базы данных.

`POSTGRES_PASSWORD` -- пароль к базе данных.

Переменные для образа с Django:

`SECRET_KEY` -- обязательная секретная настройка Django. Это соль для генерации хэшей. Значение может быть любым, важно лишь, чтобы оно никому не было известно. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#secret-key).

`DEBUG` -- настройка Django для включения отладочного режима. Принимает значения `TRUE` или `FALSE`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-DEBUG).

`ALLOWED_HOSTS` -- настройка Django со списком разрешённых адресов. Если запрос прилетит на другой адрес, то сайт ответит ошибкой 400. Можно перечислить несколько адресов через запятую, например `127.0.0.1,192.168.0.1,site.test`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#allowed-hosts).

`DATABASE_URL` -- адрес для подключения к базе данных PostgreSQL. Другие СУБД сайт не поддерживает. [Формат записи](https://github.com/jacobian/dj-database-url#url-schema).

