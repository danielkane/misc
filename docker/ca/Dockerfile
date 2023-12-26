# Use an Alpine image as the base
FROM alpine:latest

# Install EasyRSA
RUN apk update && apk add easy-rsa

# Set up a non-root user
RUN addgroup -S ca && adduser -S master -G ca

# Switch to the non-root user
USER ca

# Set the working directory
WORKDIR /home/ca

# Copy the EasyRSA vars file into the container
COPY vars /home/ca/vars

# Initialize the PKI
RUN easyrsa init-pki

# Build the CA, disable passphrase prompt
RUN echo "my_ca" | easyrsa build-ca nopass