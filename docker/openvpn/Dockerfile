# Use an alpine image as the base
FROM alpine:latest

# Install curl, openvpn, and easy-rsa
RUN apk update && apk add curl openvpn easy-rsa
RUN mkdir -p /etc/openvpn/easyrsa && cp /usr/share/easy-rsa/easyrsa /etc/openvpn/easyrsa/.
# Setup a non-root user named master with openvpn group permissions. 
RUN addgroup -S master && adduser -S master -G master
RUN chown -R master:master /etc/openvpn
USER master

# Copy your OpenVPN server configuration file into the container
# COPY server.conf /etc/openvpn/

# Copy your EasyRSA setup into the container
COPY easyrsa/ /etc/openvpn/easyrsa

# Set the working directory
WORKDIR /etc/openvpn/easyrsa

# Initialize the PKI
RUN ./easyrsa init-pki

# Grant write permissions to the pki directory
RUN chmod -R u+w /etc/openvpn/easyrsa/pki/

# Build the CA, disable passphrase prompt
RUN echo "my_ca" | ./easyrsa build-ca nopass

# Generate a CSR for the server
RUN echo "server" | ./easyrsa gen-req server nopass

# Sign the server CSR
RUN echo "yes" | ./easyrsa sign-req server server

# Make the server directory
RUN mkdir -p /etc/openvpn/server

# Copy the server certificate into the openvpn/server directory
RUN cp /etc/openvpn/easyrsa/pki/issued/server.crt /etc/openvpn/server/.

# Copy the CA certificate into the openvpn/server directory
RUN cp /etc/openvpn/easyrsa/pki/ca.crt /etc/openvpn/server/.

# Copy the server private key into the openvpn/server directory
RUN cp /etc/openvpn/easyrsa/pki/private/server.key /etc/openvpn/server/.

# Generate the tls-crypt pre-shared key
RUN openvpn --genkey --secret ta.key

# Copy the pre-shared key to the /etc/openvpn/server/ directory
RUN cp ta.key /etc/openvpn/server/

# Generate a client key and CSR
RUN echo "client1" | ./easyrsa gen-req client1 nopass

# Sign the client CSR
RUN echo "yes" | ./easyrsa sign-req client client1

# Copy the client certificate and key to the /etc/openvpn/client-configs/keys/ directory
RUN mkdir -p /etc/openvpn/client-configs/keys
RUN cp /etc/openvpn/easyrsa/pki/issued/client1.crt /etc/openvpn/client-configs/keys/
RUN cp /etc/openvpn/easyrsa/pki/private/client1.key /etc/openvpn/client-configs/keys/

# Copy the CA certificate and the tls-crypt pre-shared key to the /etc/openvpn/client-configs/keys/ directory
RUN cp /etc/openvpn/server/ca.crt /etc/openvpn/client-configs/keys/
RUN cp /etc/openvpn/server/ta.key /etc/openvpn/client-configs/keys/

# Copy the server configuration file into the /etc/openvpn/server/ directory
COPY server.conf /etc/openvpn/server/
#### RUN cp /home/master/easy-rsa/pki/private/server.key /etc/openvpn/server/

# Expose the port that OpenVPN will use
EXPOSE 1194/tcp

# Start the OpenVPN server when the container is run
CMD ["openvpn", "--config", "/etc/openvpn/server/server.conf"]