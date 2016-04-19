FROM andycunn/alpine-mono-dnx:1.0.1

ENV MONO_MANAGED_WATCHER false

# 4. link apk-installed libuv.so.1 to /usr/lib/libuv.so where Kestrel can find it
#    TODO: should try to add /usr/lib to /etc/ld.so.conf then call ldconfig rather than the hack in 4.
RUN apk update \
    && apk add --virtual=runtime-dependencies libuv \
    && ln -s /usr/lib/libuv.so.1 /usr/lib/libuv.so \
    && rm -rf /tmp/* /var/cache/apk/*

CMD [ "web" ]
