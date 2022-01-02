ARG ARCH=
FROM ${ARCH}node:16.6.1-buster AS build
WORKDIR /app
ENV DOCKER=YES
ADD . .
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends build-essential && \
    npm install && \
    npm run build && \
    npm install -g --unsafe-perm --production

# recpt1 image
FROM collelog/recpt1-build:stz2012-latest-debian-amd64 AS recpt1-image

# Final image
FROM ${ARCH}node:16.6.1-buster-slim

WORKDIR /app
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        make \
        gcc \
        g++ \
        pkg-config \
        pcscd \
        libpcsclite-dev \
        libccid \
        libdvbv5-dev \
        pcsc-tools \
        dvb-tools \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy artifacts from images / builds
COPY --from=build /usr/local/lib/node_modules/mirakurun /app
COPY --from=recpt1-image /build /

CMD ["./docker/container-init.sh"]
EXPOSE 40772 9229
