FROM ubuntu:20.04 AS asterisk16-ubuntu

LABEL maintainer="Alfred Certain <alfred.certain@gecko.co>"

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get -y update \
&& apt-get -y upgrade \
&& apt-get -y install apt-utils cmake make gcc curl tzdata \
&& apt-get -y install libncurses5-dev libboost-iostreams1.71.0 libcgi-fast-perl libcgi-pm-perl libclass-accessor-perl libcwidget4 libencode-locale-perl libfcgi-perl libhtml-parser-perl libhtml-tagset-perl libhttp-date-perl libhttp-message-perl libio-html-perl libio-string-perl liblwp-mediatypes-perl libparse-debianchangelog-perl libsigc++-2.0-0v5 libsub-name-perl libtimedate-perl liburi-perl libxapian30 libcgi-fast-perl libcgi-pm-perl libclass-accessor-perl libcwidget4 libencode-locale-perl libfcgi-perl libhtml-parser-perl libhtml-tagset-perl libhttp-date-perl libhttp-message-perl libio-html-perl libio-string-perl liblwp-mediatypes-perl libparse-debianchangelog-perl libsigc++-2.0-0v5 libsub-name-perl libtimedate-perl liburi-perl libxapian30 binutils-dev bison build-essential doxygen flex freetds-dev graphviz libc-client2007e-dev libcap-dev libcfg-dev libcodec2-dev libcpg-dev libcurl4-openssl-dev libedit-dev libfftw3-dev libgmime-2.6-dev libgmime-3.0-dev libgsm1-dev libical-dev libiksemel-dev libjansson-dev libmysqlclient-dev libneon27-dev libnewt-dev libogg-dev libosptk-dev libpopt-dev libpq-dev libradcli-dev libresample1-dev libsndfile1-dev libsnmp-dev libsqlite3-dev libsrtp2-dev libssl-dev libunbound-dev liburiparser-dev libvorbis-dev libxml2-dev libxslt1-dev pkgconf portaudio19-dev unixodbc-dev uuid-dev xmlstarlet zlib1g-dev \
&& apt-get -y install git wget vim pwgen gzip rsync mariadb-client \
&& apt-get -y install odbcinst odbcinst1debian2 libodbc1 \
&& apt-get -y install ssmtp sox lame mc screen \
&& apt-get -y autoremove \
&& apt-get clean

RUN cd /usr/src \
  && wget https://dev.mysql.com/get/Downloads/Connector-ODBC/8.0/mysql-connector-odbc_8.0.21-1ubuntu20.04_amd64.deb \
  && dpkg -i mysql-connector-odbc_8.0.21-1ubuntu20.04_amd64.deb \
  && rm -f mysql-connector-odbc_8.0.21–1ubuntu20.04_amd64.deb

RUN cd /usr/src \
  && wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-16-current.tar.gz \
  && tar xfz asterisk-* \
  && rm -f asterisk-*.tar.gz \
  && mv asterisk-* asterisk \
  && cd asterisk \
  && ./configure --prefix=/usr --with-pjproject-bundled --with-jansson-bundled --with-ssl=ssl --with-srtp  CFLAGS='-O2 -DNDEBUG' \
  && make menuselect/menuselect menuselect-tree menuselect.makeopts \
  && menuselect/menuselect --disable BUILD_NATIVE \
                         --enable app_confbridge \
                         --enable app_fax \
                         --enable app_macro \
                         --enable codec_opus \
                         --enable codec_silk \
                         --enable BETTER_BACKTRACES \
                         --disable MOH-OPSOUND-WAV \
                         --enable MOH-OPSOUND-GSM \
                         --disable res_config_pgsql \
                         --disable cel_pgsql \
                         --disable chan_phone \
                         --disable chan_skinny \
                         --disable chan_mgcp \
                         --disable chan_oss \
                         --disable chan_unistim \
  && make \
  && make install \
  && make samples \
  && make config \
  && make install-logrotate \
  && echo "asterisk -rvvvvvvvvvv" > /usr/local/sbin/ast \
  && ldconfig

EXPOSE 5038 4445 3306 5060 5061 5160 5161 4569 8088 8089 10000-20000/udp 10000-20000/tcp

COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh
WORKDIR /etc/asterisk
ENTRYPOINT ["/docker-entrypoint.sh"]
