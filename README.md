## Соответствие с ТЗ:
- [x] Бот должен быть написан на языке Python
- [x] Все настройки бот должен брать из переменных окружения или .env файла
- [x] Бот и необходимые ему сервисы должны разворачиваться в контейнерах с помощью Docker Compose
- [x] Зависимости бота указаны в requirements.txt с версиями или с помощью инструментов вроде Poetry
- [x] Для бота есть инструкция по его развёртыванию в README.md проекта
- [x] Бот логгирует свою работу с использованием библиотеки logging или loguru
- [x] Перезапуск контейнеров не должен приводить к потере данных
- [x] Бот работает и в личных сообщениях и при добавлении в чат
- [x] По команде /start бот встречает пользователя сообщением-приветствием, которое рассказывает о функционале бота
- [x] При получении сообщения с ссылкой, бот присылает сообщение-заглушку, о том что запрос принят, и запускает процесс получения скриншота в фоне
- [x] Когда скриншот получен, бот редактирует сообщение-заглушку: 
  - [x] Прикрепляет скриншот к сообщению
  - [x] Заменяет текст сообщения на заголовок сайта, URL и время обработки страницы
  - [x] (бонус 1 балл) добавляет к сообщению кнопку “Подробнее”, которая показывает WHOIS сайта
- [x] Скриншоты бот так же сохраняет в файловую систему. В имени файла обязательно должны быть: дата запроса, user_id пользователя, домен из url запроса
- [ ] (бонус 5 баллов) Бот сохраняет статистику о своей работе в базу данных - PostgreSQL или ClickHouse (Бонусные баллы за статистику начисляются по секретным правилам, 5 маскимально возможное число)
  - [x] - Сохраняет в PostgreSQL
  - [x] - есть endpoint статистики
  - [x] - сохранеие пользователя
  - [x] - сохранение скриншота, где user_id - foreignKey   
- [x] (бонус 1 балл) Бот позволяет выбрать язык работы - русский или английский. После переключения языка все последующие сообщения от бота выводятся на выбранном языке
- [x] Документация
- [x] Админ панель
- [x] Просмотр статистики
- [x] Клиент-сервер апликуха, где сервер - REST FULL API
- [x] Обработка ошибок при получении скриншота, которые уведомляют пользователя о проблемности сайта

# Подготовка
1.    git clone https://github.com/ValiaIsNotProgrammer/truepositive_test_task
   
2.    cd truepositive_test_task/
   

# Установка (не через контейнеры)
1.  Установите [poetry](https://python-poetry.org/)
2.  В .env файле поменяйте BACKEND_URL="http://localhost:8000/" и POSTGRES_HOST="localhost"
3.  ```bash
    psql -U postgres -c "CREATE DATABASE truepositive;"
    ```
> [!TIP]
> Если psql не устраивает:
> ```bash
> docker run --rm --volume pgdata:/var/lib/postgresql/data --name pg --env-file ./.env -d -p 5432:5432 postgres:14-alpine
> ```

4. ```bash
   poetry install
   poetry shell
   ```
    
5. ```bash
   python api/manage.py migrate
   python api/manage.py runserver
   ```
6. ```bash
   python bot/main.py
   ```
   

# Установка (через контейнеры)
1.    docker-compose up
   
   
После установки, переходим на https://t.me/TruePositiveTestTaskBot
Прописывает боту в сообщениях /start



> [!WARNIGN]
> ## Пара слов об архитектуре
### В начале я хотел пойти по классическому сценарию: создать класс Repo, где будет инстанс нашей БД, для оптимизации запросов, и отдельный Класс для создания скриншотов, и упаковать это дело в одну директорию
### Но я решил пойти чуть по-другому, и явно отделить бота как фронтенд, а сервер - как REST FULL API
### Из-за коротких сроков, я, к сожалению, не успел сделать аутентификацию, да и ручки выглядят сыро. Была мысль подкрутить селери, но, в одном из коммитов, я понял, что мой ендпоинт создания /screenshots, не позволит это нормально сделать
### были мысли сделать для этого ендпоинта сокет, но не успел протестировать
### И, к сожаления, под конец проекта, в одном из коммитов, моя архитектура bot.utils.connector.api_connector стала выглядить не очень: разные слои абстракции, множественные возращаемые типы данных в методах и другое

