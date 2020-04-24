## Домашнее задание  
  
Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)      
Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.    
Ответьте на вопрос: Можно ли в контейнере собрать ядро?
Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.  

## Решение
1.Разница между контейнером и образом, заключается в том что образ это коробра для контейра из которого и разворачивается контейнер,  

2.Можно ли в контейнере собрать ядро?   
Ответ: думаю да, также скачать исходники или зааттачить из хоста, вместе с `config` текущего ядра, дальше сборка ядра по сценарию, как на обычной ОС. Можно ли запустить контейнер на альтернативном ядре, слышал что да, но не интересовался этой темой, думаю это собирается в `Dockerfile`. 

Установка `docker` [офиц. здесь](https://docs.docker.com/engine/install/centos/) и [здесь](https://1cloud.ru/help/linux/instruktsiya-docker-na-centos7)    
```
sudo yum check-update
curl -fsSL https://get.docker.com/ | sh
sudo usermod -aG docker <имя пользователя> 
sudo systemctl start docker
```
Установка `docker-compose` [здесь](https://docs.docker.com/compose/install/)    
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

Cодержание `Dockerfile`:      
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
#  -t [name]  - это наименование будущего image 
#  .          - обозначает текущую директория
#  --no-cache - значит не использовать кэш предыдущих сборок
```

 
Для проверки загруженного контейнера на репозиторий можно выполнить  
dockerhub: [docker pull dbudakov/lesson:lesson_nginx](https://hub.docker.com/repository/docker/dbudakov/lesson/tags?page=1)   
```
docker run -d dbudakov/lesson:lesson_nginx
curl $(docker inspect --format {{.NetworkSettings.IPAddress}} $(docker ps|awk '/dbudakov/ {print $1}'))
```
#### Дополнительные материалы    
```
docker tag [image] [repository]:[image]  # затагировать image для заливки  
docker push [repository]:[image]         # залить image 
```
