FROM rust:alpine

# Install required system dependencies
RUN apk add --no-cache openssl openssl-libs-static openssl-dev musl-dev build-base pkgconf alpine-sdk sudo

# Create non-root user
RUN addgroup -g 1000 proxyauth && \
    adduser -D -u 1000 -G proxyauth -h /home/proxyauth proxyauth

# Set a password for the 'proxyauth' user and grant sudo permissions.
# This is specifically used to allow exporting the build sync from inside the Docker container.
# You may uncomment the following line if you need to run commands manually, 
# $ proxyauth sync export

# but this practice is not recommended for production environments.
# Instead of running commands manually, it is recommended to use a Docker registry
# and deploy by duplicating containers from a prebuilt image.

#RUN echo "proxyauth:proxyauth" | chpasswd
#RUN echo "proxyauth ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Install proxyauth from crates.io (adapt version if needed)
RUN cargo install proxyauth --root /usr/local  -j $(($(nproc) - 1)) --locked

RUN mkdir -p /etc/proxyauth/config && \
    mkdir -p /etc/proxyauth/certs && \
    mkdir -p /etc/proxyauth/import && \
    openssl req -x509 -newkey rsa:2048 -sha256 -days 1 \
            -nodes \
            -keyout /etc/proxyauth/certs/key.pem \
            -out /etc/proxyauth/certs/cert.pem \
            -subj "/CN=localhost" && \
    chown -R proxyauth:proxyauth /etc/proxyauth

# prepare user environnement
RUN proxyauth prepare

# switch user
USER proxyauth

# Expose the service port
EXPOSE 8080

# Launch the binary
CMD ["/usr/local/bin/proxyauth"]
