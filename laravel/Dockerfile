FROM php:8.2-cli

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    zip \
    && docker-php-ext-install zip pdo pdo_mysql

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /app

# Copy project files
COPY . .

# Install dependencies
RUN composer install --no-interaction --prefer-dist --optimize-autoloader --no-dev

# Set permissions for Laravel
RUN chmod -R 775 storage bootstrap/cache

# Create .env from .env.example if it doesn't exist
RUN if [ ! -f .env ]; then cp .env.example .env; fi

# Expose port
EXPOSE 8000

# Start Laravel
CMD php artisan config:cache && php artisan route:cache && php artisan serve --host=0.0.0.0 --port=8000
