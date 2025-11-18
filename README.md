# Инструкция по развёртыванию

## Требования
- Linux или WSL2 (файловая система Windows сильно замедляет работу Docker).
- Установленные Docker и Docker Compose.

## Шаги
1. Если нужно сменить порты обращения, скопируйте в корне проекта файл ``.env`` на основе ``.env.example`` и измените переменные окружения на нужные вам порты. Учтите, что в остальных частях приложения (например, клиентской части) порты нужно менять вручную.
   ```bash
   cp .env.example .env
   ```
2. Загрузите зависимости для фронтенда:
   ```
   docker compose build frontend
   docker compose run --rm frontend npm install
   ```
3. Соберите и запустите сервисы. Или пересоберите: с флагом ``--build``:
   ```bash
   docker compose up -d
   ```
4. Когда все сервисы запущены, подготовьте бэкенд внутри контейнера PHP:
   ```bash
   cp backend/.env.example backend/.env
   docker compose run --rm backend composer install
   docker compose run --rm backend php artisan key:generate
   docker compose run --rm backend php artisan migrate --seed
   ```

   Если возникает ошибка, связанная с правами на файлы и папки, выполните следующие команды:
   ```bash
   docker compose run --rm backend chown -R www-data:www-data storage bootstrap/cache
   docker compose run --rm backend chmod -R 775 storage bootstrap/cache
   ```
