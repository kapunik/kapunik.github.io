---
layout: post
title: Мое первое неудавшееся приложение
---

Первым делом, мне было необходимо выяснить, как же выкатить сайт на протоколе **https://**
Я собираюсь в будущем использовать веб-сокеты, и непомешало бы ипользовать их на протоколе **wss://**

Как запустить сервер на 3000 порту, я выснил из "Hello World!" с официального сайта Экспресса. 

Для того что-бы найти решение для порта 443, пришлось покопать документацию. И вот что я там накопал:

Я создал файлик **app.js** со следующим содержанием:
```javascript
var fs = require('fs'), // модуль для работы с файловой системой сервера
    https = require('https'), 
    express = require('express');

var port = 443;
var app = express();

var options = {
    key: fs.readFileSync('/var/www/cert/ca.key'), 	// ключ и сертификат, которые я выпустил
    cert: fs.readFileSync('/var/www/cert/ca.crt'), 	// для своего сайта, при помощи Let's Encrypt
    requestCert: true
};

app.get('/', function (req, res) {
  res.send('Hello World!');
});

var server = https.createServer(options, app);
server.listen(port, function() {
	console.log("Express server listening on port " + port);
});

```

В консоли, для запуска приложения на портах 80/443, нужно запускать ноду через **sudo**. Я до этого не сразу допёр, и минут 10 не мог понять, почему же не работает? В консоли выкатывало вот эти ошибки:
```bash
$ nodejs app.js
events.js:141
      throw er; // Unhandled 'error' event
      ^

Error: listen EACCES 0.0.0.0:443
    at Object.exports._errnoException (util.js:870:11)
    at exports._exceptionWithHostPort (util.js:893:20)
    at Server._listen2 (net.js:1224:19)
    at listen (net.js:1273:10)
    at Server.listen (net.js:1369:5)
    at Object.<anonymous> (/var/www/domains/***/app.js:19:8)
    at Module._compile (module.js:410:26)
    at Object.Module._extensions..js (module.js:417:10)
    at Module.load (module.js:344:32)
    at Function.Module._load (module.js:301:12)
```

После перезапуска с sudo - всё ок:
```bash
$ sudo nodejs app.js
Express server listening on port 443
```
# Шаблонизатор

В роли шаблонизатора я собираюсь использовать **Pug**
```bash
$ npm install pug --save
```

Я добавил одну новую строчку строчку в **app.js**, и немножко именил старую функцию //app.get//
```javascript
app.set('view engine', 'pug'); //объявление шаблонизатора

app.get('/', function (req, res) {
  res.render('index', { title: 'Knight Talks', message: 'o___O'});
});
```

В каталоге проекта создал папку **views** а в ней файл **index.pug** следующего содержания (собственно сам шаблон):
```html
html
  head
    title!= title
  body
    h1!= message
```

Волшебным образом всё это превратилось в:
```html
<html>
	<head>
		<title>Knight Talks</title>
	</head>
	<body>
		<h1>o___O</h1>
	</body>
</html>
```
![o_O](/images/o_O.png)


А потом я всё удалил. Осознал, что для криворуких мытарств - сервер арендовать - накладненько. Начинаю всё с начала, на локалхосте хД
