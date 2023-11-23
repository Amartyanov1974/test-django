# Django site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри конейнера Django запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).

## Подготовительные работы

Заходим в директорию с Dockerfile (backend_main_django) и создаем образ
```
docker build -t local-image:tagname .
```
Создаем на [dockerhub](https://hub.docker.com/) репозиторий.<br>
![Снимок экрана_2023-11-23_21-26-46](https://github.com/Amartyanov1974/test-django/assets/74543172/08f074b2-d606-4db3-9b7a-cc815aacb002)


Переименовываем образ:<br>
```
docker tag local-image:tagname new-repo:tagname
```

Авторизуемся в терминале:
```
docker login
```
При первой авторизации будет запрос логина и пароля.<br>
![Снимок экрана_2023-11-23_21-16-17](https://github.com/Amartyanov1974/test-django/assets/74543172/41faf354-9760-4eaa-a89d-d0bb39745a93)


Запушим образ в репозиторий<br>
```
docker push new-repo:tagname
```
## Подключение к Yandex.Cloud

Устанавливаем программное обеспечение для работы с Yandex.Cloud:
```
curl -sSL https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash
```
На [сайте](https://console.cloud.yandex.ru) находим свой ID<br>

Подключаемся к облаку<br>
```
yc managed-kubernetes cluster get-credentials --id <Ваш_ID> --external
```
## Установка
Переходим в директорию deploy.<br>
Прописываем в djangoapp-configmap.yaml свои значения SECRET_KEY, DATABASE_URL и ALLOWED_HOSTS.

Разворачиваем проект командой:

```
kubectl apply -f ./
```

Заходим по [ссылке](https://edu-reverent-mestorf.sirius-k8s.dvmn.org/admin) и наблюдаем свой сайт<br>

![Снимок экрана_2023-11-23_21-23-15](https://github.com/Amartyanov1974/test-django/assets/74543172/6ae29299-a688-485a-afba-feac8d9bcb4a)


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


`SECRET_KEY` -- обязательная секретная настройка Django. Это соль для генерации хэшей. Значение может быть любым, важно лишь, чтобы оно никому не было известно. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#secret-key).

`DEBUG` -- настройка Django для включения отладочного режима. Принимает значения `TRUE` или `FALSE`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-DEBUG).

`ALLOWED_HOSTS` -- настройка Django со списком разрешённых адресов. Если запрос прилетит на другой адрес, то сайт ответит ошибкой 400. Можно перечислить несколько адресов через запятую, например `127.0.0.1,192.168.0.1,site.test`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#allowed-hosts).

`DATABASE_URL` -- адрес для подключения к базе данных PostgreSQL. Другие СУБД сайт не поддерживает. [Формат записи](https://github.com/jacobian/dj-database-url#url-schema).

