FROM ubuntu:14.04

#Based on: https://bitbucket.org/jmar71n/docker-freepbx/src/bd2b09b8ec9f759b2e6fadf0f219b58558bdfddc/Dockerfile?at=master&fileviewer=file-view-default
#Guide: http://wiki.freepbx.org/display/FOP/Installing+FreePBX+13+on+Ubuntu+Server+14.04.2+LTS
#Asterisk: https://wiki.asterisk.org/wiki/pages/viewpage.action?pageId=4817506
#WebRTC: https://wiki.asterisk.org/wiki/display/AST/WebRTC+tutorial+using+SIPML5?focusedCommentId=32376196#comment-32376196
#sipml5: http://www.sipml5.org/call.htm?svn=240
#jsSIP: http://jssip.net/documentation/misc/interoperability/

ENV DEBIAN_FRONTEND noninteractive
ENV ASTERISKUSER asterisk
ENV ASTERISK_DB_PW Password
ENV ASTERISKVER 13.1
ENV FREEPBXVER 12.0.43

RUN mkdir -p /usr/src/asterisk
WORKDIR /usr/src/asterisk

RUN apt-get update && \
  apt-get install -y autoconf automake axel binutils-dev build-essential curl \
doxygen freetds-dev git libasound2-dev libbluetooth-dev libc-client-dev \
libcorosync-dev libcurl4-openssl-dev libedit-dev libgmime-2.6-dev \
libgsm1-dev libgtk2.0-dev libical-dev libiksemel-dev libjack-dev libjansson-dev \
libldap-dev liblua5.1-0-dev libmyodbc libmysqlclient-dev libmysqlclient15-dev \
libncurses-dev libncurses5-dev libneon27-dev libnewt-dev libogg-dev libh323plus-dev \
libpopt-dev libpq-dev libradiusclient-ng-dev libresample-dev libsnmp-dev \
libspandsp-dev libspeex-dev libspeexdsp-dev libsqlite0-dev libsqlite3-dev \
libsrtp-dev libsrtp0-dev libssl-dev libtool libusb-dev libvorbis-dev libvpb-dev \
libxml2-dev libxslt1-dev libz-dev lua5.1 mpg123 mysql-client pkg-config \
portaudio19-dev sox sqlite3 subversion unixodbc-dev uuid uuid-dev wget

#Download all needed items
RUN axel http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz && \
  axel http://downloads.asterisk.org/pub/telephony/libpri/libpri-current.tar.gz && \
  axel http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-13-current.tar.gz && \
  axel -o jansson.tar.gz http://www.digip.org/jansson/releases/jansson-2.7.tar.gz && \
  wget https://github.com/pjsip/pjproject/archive/2.10.tar.gz

#install pjproject
  RUN mv ./2.10.tar.gz ./pjproject-2.10.tar.gz

  RUN tar -xf ./pjproject-2.10.tar.gz && \
    rm -f pjproject-2.10.tar.gz && \
    cd pjproject-2.10 && \
    CFLAGS='-DPJ_HAS_IPV6=1' ./configure --enable-shared --disable-sound \
    --disable-resample --disable-video --disable-opencore-amr && \
    make dep && \
    make && \
    make install

#Install jansson
WORKDIR /usr/src/asterisk
RUN tar vxfz jansson.tar.gz && \
  rm -f jansson.tar.gz && \
  cd jansson-* && \
  autoreconf -i && \
  ./configure && \
  make && \
  make install

#Install asterisk
RUN tar xvfz asterisk-13-current.tar.gz && \
  rm -f asterisk-13-current.tar.gz && \
  cd asterisk-* && \
  PTLIB_CONFIG=/usr/lib/pkgconfig ./configure && \
  contrib/scripts/get_mp3_source.sh && \
	make menuselect.makeopts && \
  make && \
  make install && \
  make config && \
  ldconfig && \
  update-rc.d -f asterisk remove

# Download extra sounds
WORKDIR /var/lib/asterisk/sounds
RUN axel http://downloads.asterisk.org/pub/telephony/sounds/asterisk-core-sounds-en-wav-current.tar.gz \
  && axel http://downloads.asterisk.org/pub/telephony/sounds/asterisk-core-sounds-en-g722-current.tar.gz \
	&& axel http://downloads.asterisk.org/pub/telephony/sounds/asterisk-extra-sounds-en-wav-current.tar.gz \
  && axel http://downloads.asterisk.org/pub/telephony/sounds/asterisk-extra-sounds-en-g722-current.tar.gz \
	&& ls *.tar.gz | xargs -L 1 tar -zxf \
	&& rm -f asterisk-*-sounds-en-*-current.tar.gz
