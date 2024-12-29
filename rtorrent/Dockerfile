FROM alpine:3

ARG VERSION

RUN set -ex && \
    apk add --no-cache --virtual .build-deps \
        autoconf \
        automake \
        build-base \
        curl \
        curl-dev \
        libcurl \
        libsigc++3-dev \
        libtool \
        linux-headers \
        ncurses-dev \
        openssl-dev \
        pkgconf \
        tinyxml2-dev \
        zlib-dev \
        tar && \
    # building libtorrent
    mkdir -p /tmp/libtorrent && cd /tmp/libtorrent && \
    curl -sSL https://github.com/rakshasa/libtorrent/archive/refs/tags/v$VERSION.tar.gz | tar xz --strip 1 && \
    autoreconf -iv && \
    ./configure --prefix=/usr \
                --disable-debug \
                --disable-instrumentation && \
    make && make install && \
    # building rtorrent
    mkdir -p /tmp/rtorrent && cd /tmp/rtorrent && \
    curl -sSL https://github.com/rakshasa/rtorrent/archive/refs/tags/v$VERSION.tar.gz | tar xz --strip 1 && \
    autoreconf -iv && \
    ./configure --prefix=/usr \
                --sysconfdir=/etc \
                --mandir=/usr/share/man \
                --localstatedir=/var \
                --enable-ipv6 \
                --disable-debug \
                --with-xmlrpc-tinyxml2 && \
    make && make install && cd && \
    runDeps="$( \
        scanelf --needed --nobanner /usr/bin/rtorrent \
            | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
            | xargs -r apk info --installed \
            | sort -u \
    )" && \
    apk add --no-cache --virtual .run-deps $runDeps && \
    apk del .build-deps && \
    rm -rf /tmp/*
