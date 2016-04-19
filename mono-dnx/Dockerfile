FROM alpine:3.3

# Prevent `dnu restore` from stalling (gh#63, gh#80)
ENV MONO_THREADS_PER_CPU 50
ENV MONO_VERSION 4.4.0.40-2
ENV DNX_VERSION 1.0.0-rc1-update1
ENV DNX_USER_HOME /opt/dnx
ENV PATH $PATH:$DNX_USER_HOME/runtimes/default/bin

# Here we use several hacks collected from https://github.com/gliderlabs/docker-alpine/issues/11:
# 1. install GLibc (which is not the cleanest solution at all)
# 2. hotfix /etc/nsswitch.conf, which is apperently required by glibc and is not used in Alpine Linux
# 3. install mono package from Arch Linux
WORKDIR /tmp
RUN apk update \
    && apk add --virtual=build-dependencies curl ca-certificates tar xz \
    && curl -O "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-2.21-r2.apk" \
    && curl -O "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-bin-2.21-r2.apk" \
    && apk add --virtual=runtime-dependencies --allow-untrusted bash glibc-2.21-r2.apk glibc-bin-2.21-r2.apk \
    # TODO: remove bash from runtime requirements?
    && /usr/glibc/usr/bin/ldconfig /lib /usr/glibc/usr/lib \
    && echo 'hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4' >> /etc/nsswitch.conf \
    && curl https://archive.archlinux.org/packages/m/mono/mono-$MONO_VERSION-x86_64.pkg.tar.xz | tar xJf - -C / \
    && mozroots --import --ask-remove \
    && curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_USER_HOME=$DNX_USER_HOME DNX_BRANCH=v$DNX_VERSION sh \
    && bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
    	&& dnvm install $DNX_VERSION -alias default \
    	&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default" \
    && apk del build-dependencies \
    && rm -rf /tmp/* /var/cache/apk/*

ENTRYPOINT [ "dnx", "run" ]
