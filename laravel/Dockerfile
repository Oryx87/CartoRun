FROM php:8.2-cli

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    libsqlite3-dev \
    zip \
    curl \
    sqlite3 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install zip pdo pdo_mysql pdo_sqlite

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /app

# Copy Laravel folder
COPY laravel/ .

# Remove composer.lock to force fresh install
RUN rm -f composer.lock

# Install dependencies - skip all scripts to avoid errors
RUN composer install --no-interaction --no-dev --no-scripts --ignore-platform-reqs

# Set permissions for Laravel
RUN mkdir -p storage/framework/{sessions,views,cache} \
    && mkdir -p storage/logs \
    && mkdir -p bootstrap/cache \
    && mkdir -p database \
    && touch database/database.sqlite \
    && chmod -R 777 storage bootstrap/cache database

# Expose port
EXPOSE ${PORT:-8000}

# Start Laravel - create .env first
CMD cp .env.example .env 2>/dev/null || echo "APP_KEY=" > .env && \
    php artisan key:generate --force && \
    php artisan config:cache && \
    php artisan route:cache && \
    php artisan migrate --force && \
    php artisan serve --host=0.0.0.0 --port=${PORT:-8000}
