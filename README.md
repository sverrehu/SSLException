# SSLException

Notes on various occurrences of `javax.net.ssl.SSLException` that I
have run into, mostly when dealing with mTLS-enabled Apache Kafka.

## Tag mismatch!

```text
Caused by: javax.crypto.AEADBadTagException: Tag mismatch!
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
