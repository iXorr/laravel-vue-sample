# Инструкция по развёртыванию

![Docker](https://img.shields.io/badge/Docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-%23777BB4.svg?style=for-the-badge&logo=php&logoColor=white)
![Laravel](https://img.shields.io/badge/Laravel-%23FF2D20.svg?style=for-the-badge&logo=laravel&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)

## Описание
Это - стартовый набор Docker-сервисов для разработки и запуска REST API приложения. Структура включает в себя предопределенные конфигурации для бэкенда, и вся разработка должна происходить в соответствующей папке - ``backend`` (volume-привязка).

## Требования
- Linux или WSL2 (файловая система Windows сильно замедляет работу Docker).
- Docker (если вы используете Docker Desktop на Windows, проверьте совместимость с WSL).

## Использование вместе с подмодулями
Клонировать как обычный репозиторий - для сохранения конфигурации сервисов. Приложение нужно добавить как ``git-submodule``.

## Версии служб
Корректируйте версию определённой службы в соответствующем Dockerfile (например, при изменении версии PHP нужно перейти в ``services/php/Dockerfile``).

## Конфигурация веб-сервера
В текущей реализации все запросы просто перенаправляются в Laravel-приложение.

## Переопределение портов обращения

Если у вас *есть* и *уже запущены* сервисы, которые занимают порты ``80``, ``3306``, конкретно для этого стартового набора вы можете переопределить порты обращения. Скопируйте в корне проекта файл ``.env`` на основе ``.env.example`` и измените в нём переменные окружения на свободные, нужные вам порты.

## Переопределение названий служб

Имена контейнеров могут конфликтовать между собой (несмотря на то, что проекты запускаются в разных директориях и Docker Desktop разделяет их между собой, имена контейнеров считаются ГЛОБАЛЬНЫМИ), поэтому в файле окружения есть переменная ``APP_ID``. Она добавляет префикс для каждых новых создающихся служб.

## Начало работы
1. Для текущей реализации мы создаём подмодуль: вставляем уже существующий проект.
   ```bash
   git submodule add -b <BRANCH> <URL> backend
   ```

2. Билдим проект и устанавливаем зависимости.
   ```bash
   docker compose build
   docker compose run --rm backend composer install
   ```

3. Запускаем сервисы.
   ```bash
   docker compose up -d
   ```

4. Настраиваем окружение. 

   В папке ``backend`` скопируйте файл ``.env`` из ``.env.example`` и внесите в него следующие изменения.
   ```
   DB_CONNECTION=mysql
   DB_HOST=${DB_HOST} # docker container name
   DB_DATABASE=app_db # docker db conf
   DB_USERNAME=admin # docker db conf
   DB_PASSWORD=admin # docker db conf
   ```

   Удостоверьтесь, что сгенерирован ``APP_KEY`` и произведены миграции. Иначе, воспользуйтесь этими командами.
   ```bash
   docker compose run --rm backend php artisan key:generate
   docker compose run --rm backend php artisan migrate --seed
   ```

   И выдайте системе права для работы с папками, где сохраняются медиа-файлы или кэш.
   ```bash
   docker compose run --rm backend chown -R www-data:www-data storage bootstrap/cache
   docker compose run --rm backend chmod -R 775 storage bootstrap/cache
   ```
