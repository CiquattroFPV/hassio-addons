FROM alpine:3.10

RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base \
        cmake \
        libuv-dev \
        libffi-dev \
        python3-dev \
        openssl-dev \
        git \
    && apk add --no-cache \
        python3 \
        openssl \
        supervisor \
    && mkdir /usr/local/app \
    && git clone https://github.com/CiquattroFPV/hass_emulated_hue \
    && pip3 install \
        --no-cache-dir \
        -r hass_emulated_hue/requirements.txt \
    && cp -R hass_emulated_hue/emulated_hue /usr/local/app/ \
    && cp hass_emulated_hue/run.py /usr/local/app/run.py \
    && apk del --no-cache --purge .build-dependencies \
    && find /usr/local \
        \( -type d -a -name test -o -name tests -o -name '__pycache__' \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    && rm -f -r \
        /root/.cache \
        /root/.cmake \
        /tmp/* \
        hass_emulated_hue

# supervisord config
RUN echo $'[supervisord] \n\
nodaemon=true \n\
user=root \n\
[program:hue] \n\
command=python3 /usr/local/app/run.py \n\
autorestart=true \n\
stdout_logfile=/dev/fd/1 \n\
stdout_logfile_maxbytes=0 \n\
redirect_stderr=true' >> /usr/local/supervisord.conf

# Default volume (hassio compatible)
VOLUME /data

WORKDIR /usr/local/app
CMD ["supervisord", "-c", "/usr/local/supervisord.conf"]