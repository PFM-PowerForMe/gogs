FROM docker.io/library/alpine:edge AS git
ARG TAG
ENV TAG=${TAG:-main}
RUN apk update && apk add --no-cache \
	git
WORKDIR /
RUN git -c advice.detachedHead=false clone --branch $TAG --depth=1 --recurse-submodules https://github.com/pfm-powerforme/gogs.git source
WORKDIR /source/


FROM docker.io/library/golang:alpine AS builder
RUN apk update && apk --no-cache --no-progress add --virtual \
  build-deps \
  build-base \
  git \
  linux-pam-dev
WORKDIR /
COPY --from=git /source/ build
WORKDIR /build/
RUN docker/build/install-task.sh
RUN TAGS="cert pam" task build


FROM docker.io/library/alpine:latest AS runtime
RUN apk update && apk --no-cache --no-progress add \
  bash \
  ca-certificates \
  curl \
  git \
  linux-pam \
  openssh \
  s6 \
  shadow \
  socat \
  tzdata \
  rsync
ENV GOGS_CUSTOM=/data/gogs
COPY --from=git /source/docker/nsswitch.conf /etc/nsswitch.conf
WORKDIR /app/gogs
COPY --from=git /source/docker/ docker/
COPY --from=builder /build/gogs gogs
RUN docker/build/finalize.sh
VOLUME ["/data", "/backup"]
EXPOSE 22 3000
HEALTHCHECK CMD (curl -o /dev/null -sS http://localhost:3000/healthcheck) || exit 1
ENTRYPOINT ["/app/gogs/docker/start.sh"]
CMD ["/usr/bin/s6-svscan", "/app/gogs/docker/s6/"]
