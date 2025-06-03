# Stage 1: Build dependencies and vendor
FROM php:8.2-cli as build

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    libxml2-dev \
    libcurl4-openssl-dev \
    python3 \
    python3-pip \
    pkg-config \
    build-essential \
    libhdf5-dev \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install zip mysqli curl xml

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /app
COPY . /app

# Install PHP dependencies
RUN composer install --no-dev --optimize-autoloader

# Install Python dependencies
RUN pip3 install --upgrade pip setuptools && \
    pip3 install -r requirements.txt

# Stage 2: Production image
FROM php:8.2-cli-slim

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    libzip4 \
    libxml2 \
    libcurl4 \
    python3 \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install zip mysqli curl xml

WORKDIR /var/www/html

# Copy built app from previous stage
COPY --from=build /app /var/www/html

# Expose port
EXPOSE 80

# Increase upload size (if needed, but php.ini may not exist in cli image)
RUN echo "post_max_size = 4000M\nupload_max_filesize = 4000M" > /usr/local/etc/php/conf.d/uploads.ini

# Start Laravel server
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=80"]
