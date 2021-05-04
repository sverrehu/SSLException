# SSLException

Notes on various occurrences of `javax.net.ssl.SSLException` that I
have run into, mostly when dealing with mTLS-enabled Apache Kafka.

## Tag mismatch!

```text
Caused by: javax.crypto.AEADBadTagException: Tag mismatch!
```

I have seen this exception a couple of times, both related to using a
certificate for more than it is made to allow.

* Using a server-only certificate in a client. Make sure "X509v3
  Extended Key Usage" contains at least "TLS Web Client
  Authentication":

  ```text
        X509v3 extensions:
            X509v3 Extended Key Usage: 
                TLS Web Client Authentication, TLS Web Server Authentication
  ```
  
* Certificate contains "X509v3 Key Usage" without allowing "Data
  Encipherment". If this extension is part of the certificate, it
  limits what the key may be used for to the usages listed. Make sure
  it contains "Data Encipherment":

  ```text
        X509v3 extensions:
            X509v3 Key Usage: 
                Digital Signature, Key Encipherment, Data Encipherment
  ```

## Java heap space

```text
java.lang.OutOfMemoryError: Java heap space
```

Not wrapped in `SSLException`, but included here since it is related
to SSL. Happens when connecting a Kafka client that assumes plainext
to a Kafka broker that expects SSL. Due to how the Kafka protocol
works, the client will interpret the start of the SSL handshake as a
protocol header, mistakenly extracting av very big length specifier,
and attempt to allocate memory beyond the possible.

## Given final block not properly padded

```text
java.security.UnrecoverableKeyException: failed to decrypt safe contents entry:
javax.crypto.BadPaddingException: Given final block not properly padded. Such issues can arise if a bad key is used during decryption.
```

Wrong keystore/truststore password.
