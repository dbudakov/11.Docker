Установка `docker`


Установка `docker-compose`

cодержание `dockerfile`:  
```
FROM alpine:3.11        # базовая архитектура контейнера
WORKDIR /home/vagrant   # рабочая директория, будут являтся точкой отсчёта для контейнера
EXPOSE 80               # открывает 80 порт для общения контейнера и хоста
RUN apk add nginx ; \   # установка nginx
mkdir /run/nginx && touch /run/nginx/nginx.pid ; \  # создание необходимых директорий
echo "daemon off;" >> /etc/nginx/nginx.conf         # настройка nginx для автономной работы
COPY default.conf /etc/nginx/conf.d/default.conf    # копия настроек nginx для тестовой страницы
COPY index.html /var/www/index.html                 # копия самой тестовой страницы
CMD /usr/sbin/nginx -c /etc/nginx/nginx.conf        # запуск nginx использою указанный конфиг

```


Для сборки контейнера набираем следующую команду:  
```
docker build -t lesson_nginx . --no-cache
## здесь:
   -t [name]  - это наименование будущего image 
   .          - обозначает текущую директория
   --no-cache - значит не использовать кэш предыдущих сборок
```

 
Для проверки загруженного контейнера на репозиторий можно выполнить

docker run -d dbudakov/lesson:lesson_nginx
curl $(docker inspect --format {{.NetworkSettings.IPAddress}} $(docker ps|awk '/dbudakov/ {print $1}'))

#### Дополнительные материалы   
```
docker tag [image] [repository]:[image]  # затагировать image для заливки  
docker push [repository]:[image]         # залить image 
```
