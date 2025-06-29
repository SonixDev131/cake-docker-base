FROM mcr.microsoft.com/devcontainers/php:1-8.4-bookworm

# Đặt thư mục làm việc hiện tại trong container là /var/www/html
WORKDIR /var/www/html

# Kích hoạt module Rewrite và SSL của Apache
# Module Rewrite cho phép bạn sử dụng các quy tắc để thay đổi URL và làm cho chúng thân thiện hơn với SEO
# SSL cho phép máy chủ hỗ trợ SSL/TLS, giúp bảo mật các kết nối HTTP bằng cách mã hóa dữ liệu truyền tải giữa máy chủ và khách hàng
RUN a2enmod rewrite ssl

RUN apt-get update && apt-get install -y \
    libssl-dev \                   
    build-essential \              
    autoconf \                     
    pkg-config \                  
    gnupg \                        
    curl \                         
    libicu-dev \                  
    unzip zip \                 
    unixodbc \                      
    unixodbc-dev \                 
    libmariadb-dev \
    openssl \          
    && rm -rf /var/lib/apt/lists/*

# Install MariaDB client
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y mariadb-client \ 
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*

# Install php-mysql driver
RUN docker-php-ext-install mysqli pdo pdo_mysql intl

# Cấu hình XDebug (XDebug đã được cài đặt sẵn trong base image)
# Chỉ cần sao chép file cấu hình XDebug từ host vào container
COPY ../xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Tạo thư mục chứa chứng chỉ SSL trong container
RUN mkdir -p /etc/ssl/private /etc/ssl/certs

# Sao chép chứng chỉ SSL vào container
COPY ../apache-config/apache-selfsigned.crt /etc/ssl/certs/apache-selfsigned.crt
COPY ../apache-config/apache-selfsigned.key /etc/ssl/private/apache-selfsigned.key

# Sao chép file cấu hình SSL từ thư mục apache-config trên máy chủ vào container
COPY ../apache-config/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment this line to install global node packages.
# RUN su vscode -c "source /usr/local/share/nvm/nvm.sh && npm install -g <your-package-here>" 2>&1

# Kích hoạt cấu hình SSL
RUN a2ensite default-ssl.conf