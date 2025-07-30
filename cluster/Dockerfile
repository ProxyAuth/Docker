FROM rust:alpine

# Install required system dependencies
RUN apk add --no-cache bash curl openssl openssl-libs-static openssl-dev musl-dev build-base pkgconf alpine-sdk sudo

# Create non-root user
RUN addgroup -g 1000 proxyauth && \
    adduser -D -u 1000 -G proxyauth -h /home/proxyauth proxyauth

# install beta version
RUN curl -fsSL https://proxyauth.app/sh/install | bash

# switch user
USER proxyauth

# Expose the service port
EXPOSE 8080

# Launch the binary
CMD ["/usr/local/bin/proxyauth"]
