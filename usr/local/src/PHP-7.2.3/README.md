Install dependencies
``` 
yum install -y git gcc gcc-c++ libxml2-devel pkgconfig openssl-devel bzip2-devel curl-devel libpng-devel libjpeg-devel libXpm-devel freetype-devel gmp-devel libmcrypt-devel mariadb-devel aspell-devel recode-devel autoconf bison re2c libicu-devel uw-imap-devel libxslt-devel readline-devel libtidy libtidy-devel
```

Clone source code
```
git clone https://github.com/php/php-src.git -b PHP-7.2.3 /usr/local/src/PHP-7.2.3/
```

Prepare sources for build
```
sudo adduser --system --no-create-home --user-group -s /sbin/nologin nginx
/usr/local/src/PHP-7.2.3 && ./buildconf --force
```

Configure build
```
./configure \
    --prefix=/opt/php/php7.2.3 \
    --sysconfdir=/opt/php/php7.2.3/etc \
    --bindir=/opt/php/php7.2.3/bin \
    --sbindir=/opt/php/php7.2.3/sbin \
    --includedir=/opt/php/php7.2.3/include \
    --libexecdir=/opt/php/php7.2.3/libexec \
    --with-config-file-path=/opt/php/php7.2.3/etc \
    --with-config-file-scan-dir=/opt/php/php7.2.3/etc/conf.d \
    --enable-gd-native-ttf \
    --enable-bcmath \
    --enable-filter \
    --enable-sockets \
    --enable-exif \
    --enable-soap \
    --enable-ftp \
    --enable-simplexml \
    --enable-xmlreader \
    --enable-xmlwriter \
    --enable-zip \
    --enable-mbstring \
    --enable-inline-optimization \
    --enable-cgi \
    --enable-calendar \
    --enable-phpdbg \
    --enable-phpdbg-webhelper \
    --enable-opcache \
    --enable-sysvmsg \
    --enable-sysvsem \
    --enable-sysvshm \
    --enable-pcntl \
    --enable-intl \
    --enable-mbregex \
    --enable-mbstring \
    --enable-mysqlnd \
    --enable-fpm \
    --with-gd \
    --with-libdir=/lib \
    --with-bz2 \
    --with-curl \
    --with-freetype-dir \
    --with-jpeg-dir \
    --with-png-dir \
    --with-mcrypt \
    --with-mysql-sock=/var/lib/mysql/mysql.sock \
    --with-mysqli=mysqlnd \
    --with-pdo-mysql=mysqlnd \
    --with-pdo-sqlite \
    --with-zlib-dir \
    --with-openssl \
    --with-kerberos \
    --with-gettext \
    --with-mysql \
    --with-mysqli \
    --with-pdo-mysql \
    --with-pdo-sqlite \
    --with-xsl \
    --with-sqlite3 \
    --with-zlib \
    --with-iconv \
    --with-xmlrpc \
    --with-tidy \
    --with-readline \
    --with-mhash \
    --with-pcre-regex \
    --with-fpm-user=nginx \
    --with-fpm-group=nginx
```

Compile
```
make -j4
```

Install
```
make install
```

Create symlinks and directories
```
cd /usr/local/bin/
ln -s /opt/php/php7.2.3/bin/php
ln -s /opt/php/php7.2.3/bin/phpize
ln -s /opt/php/php7.2.3/bin/php-config
mkdir /var/log/php/ && chown -R nginx:nginx /var/log/php/
```

Create systemd service
```
cat <<EOF > /usr/lib/systemd/system/php7-fpm.service
[Unit]
Description=The PHP 7.2.3 FastCGI Process Manager
After=network.target

[Service]
Type=simple
PIDFile=/run/php7.2.3.pid
ExecStart=/opt/php/php7.2.3/sbin/php-fpm --nodaemonize --fpm-config /opt/php/php7.2.3/etc/php-fpm.conf
ExecReload=/bin/kill -USR2 $MAINPID

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload && systemctl enable php7-fpm.service && systemctl start php7-fpm.service
```
