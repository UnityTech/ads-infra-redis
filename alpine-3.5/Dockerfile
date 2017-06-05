FROM registry2.applifier.info:5005/ads-infra-kontrol-alpine-3.5:1.0.0

ENV REDIS_VERSION=3.0.7 \
	REDIS_SHA=e56b4b7e033ae8dbf311f9191cf6fdf3ae974d1c

ADD resources/ .
RUN set -ex && \
    apk add --no-cache --virtual .transient gcc linux-headers make musl-dev tar && \
	wget -O redis.tar.gz "http://download.redis.io/releases/redis-$REDIS_VERSION.tar.gz" && \
	echo "$REDIS_SHA *redis.tar.gz" | sha1sum -c - && \
	mkdir -p /tmp/redis && \
	tar -xzf redis.tar.gz -C /tmp/redis --strip-components=1  && \
	make -C /tmp/redis && \
	make -C /tmp/redis install && \
	rm -rf /tmp/redis redis.tar.gz && \
	apk del .transient
	  