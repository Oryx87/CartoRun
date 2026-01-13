FROM php:8.2-cli

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    zip \
    curl \
    sqlite3 \
    && docker-php-ext-install zip pdo pdo_mysql pdo_sqlite \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /app

# Copy everything
COPY . .

# Remove composer.lock to force fresh install
RUN rm -f composer.lock

# Install dependencies WITHOUT running scripts
RUN composer install --no-interaction --optimize-autoloader --ignore-platform-reqs --no-scripts

# Set permissions for Laravel
RUN mkdir -p storage/framework/{sessions,views,cache} \
    && mkdir -p storage/logs \
    && mkdir -p bootstrap/cache \
    && mkdir -p database \
    && touch database/database.sqlite \
    && chmod -R 777 storage bootstrap/cache database

# Remove .env so Laravel reads environment variables from Render
RUN rm -f .env

# Expose port
EXPOSE 8000

# Run migrations and start Laravel
CMD php artisan migrate --force && php artisan serve --host=0.0.0.0 --port=8000
```

## Les changements importants :

1. **Ajout de `sqlite3`** dans les dépendances système
2. **Ajout de `pdo_sqlite`** dans les extensions PHP
3. **Création du dossier `database/`** : `mkdir -p database`
4. **Création du fichier SQLite** : `touch database/database.sqlite`
5. **Permissions sur database/** : `chmod -R 777 ... database`
6. **Migration automatique** : `php artisan migrate --force` dans le CMD

## Sur Render, vérifie aussi la variable d'environnement :
```
DB_CONNECTION=sqlite
DB_DATABASE=/app/database/database.sqlite
