ARG ALPINE_VERSION="3.24"

FROM alpine:${ALPINE_VERSION} AS build

RUN apk add --no-cache sops upx && upx --best --lzma /usr/bin/sops

FROM alpine:${ALPINE_VERSION} AS runtime

RUN apk add --no-cache \
    git && \
    rm -rf /var/cache/apk/* && \
    adduser -D app -u 40105

COPY --from=build /usr/bin/sops /usr/bin/sops

WORKDIR /home/app
USER app

ENTRYPOINT ["/bin/sh"]