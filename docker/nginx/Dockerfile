FROM nginx:1.21.0-alpine as dev

# Fixing permissions issue (mounted volume has owner with id=1000)
RUN ( \
    echo http://dl-cdn.alpinelinux.org/alpine/v3.7/community >> /etc/apk/repositories \
    && apk -U --no-cache add shadow \
    && usermod -u 1000 nginx \
    && groupmod -g 1000 nginx \
  )

RUN set -eux \
    && apk add --no-cache \
        nano

COPY ./docker/nginx/site.tpl.conf /etc/nginx/templates/default.conf.template

WORKDIR /app
