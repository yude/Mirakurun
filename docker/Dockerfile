ARG ARCH=
FROM collelog/buildenv:node16-debian AS build
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

# libarib25 image
FROM collelog/libarib25-build:epgdatacapbon-latest-debian-amd64 AS libarib25-image

# recdvb image
FROM collelog/recdvb-build:kaikoma-soft-latest-debian-amd64 AS recdvb-image

# recfsusb2n image
FROM collelog/recfsusb2n-build:epgdatacapbon-latest-debian-amd64 AS recfsusb2n-image

# arib-b25-stream-test image
FROM collelog/arib-b25-stream-test-build:latest-debian-amd64 AS arib-b25-stream-test-image

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
COPY --from=libarib25-image /build /
COPY --from=recpt1-image /build /
COPY --from=recdvb-image /build /
COPY --from=recfsusb2n-image /build /
COPY --from=arib-b25-stream-test-image /build /

CMD ["./docker/container-init.sh"]
EXPOSE 40772 9229
