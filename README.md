### Инструкция по сборке и запуску приложения
> В этом разделе нужно описать пререквизиты и примеры запуска
#### Пререквизиты:
python
pip
flask
#### Пример запуска:
1) Переходим в директорию `VulnerableApp/App`.
2) Запускаем `main.py`. `python3 main.py`
3) Копируем `http://127.0.0.1:5000` и вставляем в браузер.

### Proof of Concept
> PoC для реализованных уязвимостей нужно описать в этом разделе. Варианты реализации PoC:
> - Пошаговое руководство для ручного воспроизведения
> - CURL-команда или Bash-однострочник для программного воспроизведения
> - Скрипт-эксплойт из репозитория (достаточно сослаться на файл)

### a. XSS
  1. Сначала логинимся под любым пользователем, можно создавать своего. В примере я зашел под пользователем `user1:3241`
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/xss/xss1.png)
  2. У каждого пользователя есть возможность установить себе статус.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/xss/xss2.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/xss/xss3.png)
  4. В приложении есть XSS уязвимость, которую можно эксплуатировать передав в форму статуса следующий текст: `<script> alert('XSS Attack!'); </script>`
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/xss/xss4.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/xss/xss5.png)

### b. IDOR
      В этом приложении IDOR уязвимость заключается в том, что можно использовать горизонтальное и вертикальное повышения привелегий изменив url.
      Подробнее рассмотрим уязвимость на примере. 
      1. Логинимся под любым пользователем.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/idor/idor1.png)
      2. Меняем URL с /user/user1 на /user/admin
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/idor/idor2.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/idor/idor3.png)

### c. SQLI
    Я решил сделать 2 уязвимости, так как первая SQL-инъекция показалось мне слишком простой. 
  #### Первая уязвимость
  1. Допустим, нам известно, что логин администратора это admin. Пытаемся залогиниться за администратора.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli1/sqli1.png)
 2. Используем случайной пароль.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli1/sqli2.png)
3. Попытка оказалось неуспешной. Попробуй вместо в форме для логина после `admin` вставить `' --` 
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli1/sqli3.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli1/sqli4.png)
Попытка оказалась успешной. Это и есть первая sqli.
  #### Вторая уязвимость
1. Исследуем как работают кнопки на главной странице.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli2/sqli2_1.png)
2. Кликнем на `Dogs` и перехватим трафик с помощью `Burp Suite Intercept`
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli2/sqli2_2.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli2/sqli2_3.png)
3. Кликнем на `Dogs` и перехватим трафик с помощью `Burp Suite Intercept` ещё раз.
Изменим запрос с `GET /?category=dogHTTP/1.1` на `GET /?category=dog'+UNION+SELECT+null,null,username,null,password+FROM+users-- HTTP/1.1`
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli2/sqli2_4.png)
4. Получаем имена и пароли всех пользователей приложения. Это вторая sqli.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/sqli2/sqli2_5.png)


### d. OS command injection
1. Переходим на /ping
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/OS_Command_Injection/os1.png)
2. Убедимся, что сервис работает.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/OS_Command_Injection/os2.png)
3. Внедрим OS command injection.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/OS_Command_Injection/os3.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/OS_Command_Injection/os4.png)

### e. Path Traversal
Я попытался реализовать что-то похожее на первую лабораторную работу в https://portswigger.net/web-security/file-path-traversal.
1. Посмотрим, как подгружаются картинки на главной странице.   
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/path_traversal/path1.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/path_traversal/path2.png)
2. Переместим запрос `/laodImage?filename=cat4.jpg` в `repeater`
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/path_traversal/path3.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/path_traversal/path4.png)
3. Поменяем filename с `cat4.jpg` на `../../../../../../../etc/passwd`
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/path_traversal/path5.png)
4. У нас получилось вывести `/etc/passwd`. Это значит, что приложение имеет уязвимость.

### f. Brute force
Регистрация в этом приложении позволяет использовать пароли состоящие ровно из четырех цифр.
Понятно, что их можно перебрать, если мы знаем логин.
1. Перехватим запрос входа в `admin` со случайным паролем.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/brute/brute1.png)
2. Закинем этот запрос в `Intruder`.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/brute/brute2.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/brute/brute3.png)
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/brute/brute4.png)
3. Перебираем пароли, а потом сортируем ответы по длине. Будет единственный ответ, который отличается от остальных по длине. 
Он и содержит правильным пароль.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/brute/brute5.png)
4. Пробуем подобранный пароль и входим под учетной записью администратора.
![alt text](https://github.com/mannazarov/VulnerableApp/blob/main/readme_images/brute/brute6.png)

### Дополнительные комментарии
> Опциональный раздел

...

