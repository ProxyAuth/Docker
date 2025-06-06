FROM rust:alpine

# Install required system dependencies
RUN apk add --no-cache openssl openssl-libs-static openssl-dev musl-dev build-base pkgconf alpine-sdk sudo

# Create non-root user
RUN addgroup -g 1000 proxyauth && \
    adduser -D -u 1000 -G proxyauth -h /home/proxyauth proxyauth

# Add password for proxyauth and sudoer's permission
# it's particularity for give export the sync build (inside the docker) 
# you are possible uncomment the line if use the command
# proxyauth sync export
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
