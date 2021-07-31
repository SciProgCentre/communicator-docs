### Обмен сообщениями

----

### Воркер

Имеем DEALER сокет к прокси-серверу.

----

#### Получаем от прокси-сервера

Первая часть сообщения - UTF-8 строка, тип сообщения.

QUERY: Запрос на вычисление функции.

- ID запроса
- Аргумент запроса (байтовый блок)
- Название функции (строка)

RESPONSE_RECEIVED: Сообщение о том, что ответ на запрос получен.

- ID запроса

INCOMPATIBLE_SPECS_FAILURE: Сообщение о том, что один из кодеров воркера отличается от тех, которые уже есть у других
воркеров
(ошибка IncompatibleSpecsException).

- Название функции
- Identity кодера аргумента у других воркеров
- Identity кодера результата у других воркеров

----

#### Отправляем прокси-серверу

Первая часть сообщения - UTF-8 строка, тип сообщения.

RESPONSE_RESULT: Ответ на запрос - завершен успешно

- ID запроса
- Результат запроса (байтовый блок)

RESPONSE_EXCEPTION: Ответ на запрос - ошибка RemoteFunctionException (см. файл Exceptions.kt)

- ID запроса
- Строка remoteExceptionMessage (сообщение эксепшена, который бросила удаленная функция. можно оставить пустым, если в
  языке нет exception.toString)

QUERY_RECEIVED: Сообщение о том, что запрос получен воркером.

- ID запроса (16 байтов)

HEART_BEAT: Heart beat (зарезервировано)

- Нет дополнительных частей

WORKER_REGISTER: Запрос воркера на подключение к прокси и передача информации о всех кодерах

- Кол-во функций (4 байта)
- Далее для каждой из функций одна часть - название, вторая часть - identity кодера аргумента, третья часть - identity
  кодера результата

----