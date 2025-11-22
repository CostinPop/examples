# --- Build Stage --- 

FROM --platform=linux/x86_64 debian:bookworm AS build

# 1. Instalează OpenJDK 21 (cel mai recent LTS)
RUN set -xe ; \
    apt -yqq update ; \
    apt -yqq install openjdk-21-jdk openjdk-21-jre-headless \
    ;

# 2. Re-generează ldconfig pentru Java 21
RUN ldconfig /usr/lib/jvm/java-21-openjdk-amd64/lib/server/

WORKDIR /src

COPY ./SimpleHttpServer.java /src/SimpleHttpServer.java

RUN javac SimpleHttpServer.java

# --- Runtime Stage (minimal) ---
FROM scratch

# Copiază bibliotecile de sistem
COPY --from=build /lib/x86_64-linux-gnu/libc.so.6 /lib/x86_64-linux-gnu/libc.so.6
COPY --from=build /lib/x86_64-linux-gnu/libstdc++.so.6 /lib/x86_64-linux-gnu/libstdc++.so.6
COPY --from=build /lib/x86_64-linux-gnu/libm.so.6 /lib/x86_64-linux-gnu/libm.so.6
COPY --from=build /usr/lib/x86_64-linux-gnu/libz.so.1 /usr/lib/x86_64-linux-gnu/libz.so.1
COPY --from=build /lib/x86_64-linux-gnu/libgcc_s.so.1 /lib/x86_64-linux-gnu/libgcc_s.so.1
COPY --from=build /lib64/ld-linux-x86-64.so.2 /lib64/ld-linux-x86-64.so.2
COPY --from=build /etc/ld.so.cache /etc/ld.so.cache

# Copiază runtime-ul OpenJDK 21 (ATENȚIE la schimbarea căii)
COPY --from=build /usr/lib/jvm/java-21-openjdk-amd64/ /usr/lib/jvm/java-21-openjdk-amd64/
COPY --from=build /etc/ssl/certs/java/cacerts /etc/ssl/certs/java/cacerts
COPY --from=build /etc/java-21-openjdk/security/ /etc/java-21-openjdk/security/

# Copiază fișierul .class compilat
COPY --from=build /src/SimpleHttpServer.class /usr/src/SimpleHttpServer.class


