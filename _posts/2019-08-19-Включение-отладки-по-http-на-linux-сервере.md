---
layout: post
title:  "Включение отладки по http на linux сервере"
date:   2019-08-27 19:00:00 +0300
categories: linux debug
---

# Включение отладки по http на linux сервере

Для включения отладки по http нужно:
* Изменить скрипт запуска ragent
   * включить отладку
   * изменить режим отладки на http
* перечитать скрипт запуска
* перезапустить сервер

## Скрипт запуска ragent
Скрипт для Ubuntu по умолчанию находится в /etc/init.d/srv1cv83.
В нём нужно найти строку
```
#SRV1CV8_DEBUG=
```
и заменить на
```
SRV1CV8_DEBUG=1
```
(убрать комментарий в начале строки и поставить = 1)

и в строке 
```
 [ "x$SRV1CV8_DEBUG" == "x1" ] && cmdline="$cmdline -debug"
```
добавить `-http`
```
 [ "x$SRV1CV8_DEBUG" == "x1" ] && cmdline="$cmdline -debug -http"
```

## Перечитать конфигурацию сервера
```
sudo systemctl daemon-reload
```

## Перезапустить сверер
```
sudo service srv1cv83 restart
```

## Проверка, что всё обновилось
`sudo ps auxf`
в выводе команды 

```
 /opt/1C/v8.3/x86_64/ragent -daemon -debug -http
  \_ /opt/1C/v8.3/x86_64/rmngr -port 1541 -host toir-prod -range 1560:1591 -debug -http -clstid 1e38ac24-c807-11e9-d784-fa163e1cc1b5
  \_ /opt/1C/v8.3/x86_64/dbgs --port=1550
  \_ /opt/1C/v8.3/x86_64/rphost -range 1560:1591 -reghost toir-prod -regport 1541 -pid 1e881df4-c807-11e9-d784-fa163e1cc1b5 -debug -http
```
в параметрах запуска сервисов должно повяиться `-http`, а также новая строка сервиса отладки `/opt/1C/v8.3/x86_64/dbgs --port=1550`

