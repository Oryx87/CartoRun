FROM php:8.2-cli

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    zip \
    curl \
    && docker-php-ext-install zip pdo pdo_mysql \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /app

# Copy composer files first
COPY composer.json composer.lock* ./

# Install dependencies with verbose output
RUN composer install --no-interaction --prefer-dist --optimize-autoloader --no-dev --no-scripts || \
    composer install --no-interaction --prefer-dist --optimize-autoloader --no-dev

# Copy rest of the application
COPY . .

# Complete composer install with scripts
RUN composer dump-autoload --optimize

# Set permissions for Laravel
RUN mkdir -p storage/framework/{sessions,views,cache} && \
    mkdir -p storage/logs && \
    chmod -R 775 storage bootstrap/cache

# Create .env if needed
RUN if [ ! -f .env ]; then cp .env.example .env 2>/dev/null || echo "APP_KEY=" > .env; fi

# Expose port
EXPOSE 8000

# Start Laravel
CMD php artisan config:clear && php artisan cache:clear && php artisan serve --host=0.0.0.0 --port=8000
