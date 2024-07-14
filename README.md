# Jetty server with TLS client authentication

Example of setting up a Jetty server capable of TLS client authentication using the Jetty Maven plugin.

# Create the certificates

Create keystore for the server:

```bash
openssl genpkey -algorithm RSA -out server-key.pem -pkeyopt rsa_keygen_bits:2048
openssl req -new -x509 -key server-key.pem -out server-cert.pem -days 365 -subj "/CN=localhost"
openssl pkcs12 -export -in server-cert.pem -inkey server-key.pem -out keystore.p12 -name jetty -password pass:changeit
```

Create truststore for TLS client authentication:

```bash
openssl genpkey -algorithm RSA -out client-key.pem -pkeyopt rsa_keygen_bits:2048
openssl req -new -x509 -key client-key.pem -out client-cert.pem -days 365 -subj "/CN=client"
openssl pkcs12 -export -in client-cert.pem -inkey client-key.pem -out truststore.p12 -name jetty -password pass:changeit
```

# Building

```bash
mvn jetty:run
```

# Testing

Test with cURL:

```bash
$ curl --cert client-cert.pem --key client-key.pem --pass changeit https://localhost:8443/hello -k
<h1>Hello Servlet</h1>
session=node0nw60ne6q26i4fhf6brusvw8d0
```

Without the client certificate, authentication is denied:

```bash
$ curl https://localhost:8443/hello -k
curl: (56) LibreSSL SSL_read: LibreSSL/3.3.6: error:1404C412:SSL routines:ST_OK:sslv3 alert bad certificate, errno 0
```
