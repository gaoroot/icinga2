FROM ubuntu:18.04

ENV VER=1.9.30

#ENV TZ=Asia/Irkutsk

#RUN echo 'Acquire::http::Proxy "http://10.0.0.1:3827/";' >> /etc/apt/apt.conf
#RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y apache2 \
    gzip rsync graphviz sqlite3 libjpeg-turbo8-dev \
    libpng-dev php-sqlite3 php-gettext php-curl    \ 
    libfreetype6-dev openssl libapache2-mod-php && \
    ln -sfT /dev/stderr "/var/log/apache2/error.log" && \
    ln -sfT /dev/stdout "/var/log/apache2/access.log"


ADD http://www.nagvis.org/share/nagvis-${VER}.tar.gz /

RUN tar -zxvf /nagvis-${VER}.tar.gz && cd /nagvis-${VER} && \
    ./install.sh -q -p /usr/local/nagvis -l "tcp:icinga:6558" -b mklivestatus -u www-data -g www-data -w /etc/apache2/conf-available -a y && \
    cp /nagvis-${VER}/nagvis-make-admin /usr/local/nagvis/etc/ && \
    printf "admin:$(openssl passwd -crypt admin)\n" >> /etc/apache2/htpasswd.users 

# There is not way to create this on preparition config, auth.db is create on first time with the apache.
#    cd /usr/local/nagvis/etc && ./nagvis-make-admin nagvis

ADD nagvis.conf /etc/apache2/conf-available/nagvis.conf
ADD nagvis.ini.php /usr/local/nagvis/etc/nagvis.ini.php
RUN ln -s /etc/apache2/conf-available/nagvis.conf /etc/apache2/conf-enabled/nagvis.conf

CMD /usr/sbin/apache2ctl -D FOREGROUND