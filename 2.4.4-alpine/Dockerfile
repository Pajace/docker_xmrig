FROM alpine:3.7

RUN apk add --no-cache ca-certificates

RUN set -ex \
    && apk add --no-cache --virtual .fetch-deps gnupg tar gzip \
    && wget https://github.com/xmrig/xmrig/archive/v2.4.4.tar.gz \
    && mkdir -p /usr/src/xmrig \
    && tar -xzC /usr/src/xmrig --strip-components=1 -f v2.4.4.tar.gz \
    && rm v2.4.4.tar.gz \
    && apk add --no-cache --virtual .build-deps  \
                bzip2-dev \
                coreutils \
                dpkg-dev dpkg \
                expat-dev \
                gcc \
                g++ \
                gdbm-dev \
                libc-dev \
                libffi-dev \
                ncurses-dev \
                pax-utils \
                readline-dev \
                sqlite-dev \
                tcl-dev \
                xz-dev \
                zlib-dev \
                make \
                cmake \
                linux-headers \
				libuv-dev \
				libmicrohttpd-dev \
# add build deps before removing deps in case there's overlap				
	&& apk del .fetch-deps \
	\
    && cd /usr/src/xmrig/ \
	&& sed -i 's/ kDonateLevel = 5/ kDonateLevel = 0/g' src/donate.h \
    && mkdir build \
    && cd build \
    && cmake .. \
    && make -j $(nproc) \
	&& cp xmrig /usr/local/bin/ \
# only keep dependency
    && runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
            | tr ',' '\n' \
            | sort -u \
            | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
        )" \
    && apk add --virtual .aeon-rundeps $runDeps \
    && apk del .build-deps \
    \
    && rm -rf /usr/src/xmrig