# Capítulo 46: Curl 8.20.0

## 46.1 — Objetivo

An URL retrival utility and library.

## 46.2 — Dependencias 

- libpsl (Cap. 43)
- nghttp2 (Cap. 44)

## 46.3 — Descargar

```bash
wget https://curl.se/download/curl-8.20.0.tar.xz
```

## 46.4 — Compilar

```bash
cd /sources
tar xvf curl-8.20.0.tar.xz
cd curl-8.20.0

sed -i 's/2F 5C/%2F %5C/' scripts/wcurl

mkdir -p build && cd build

../configure --prefix=/usr --disable-static --with-openssl --with-ca-path=/etc/ssl/certs
make -j$(nproc)

make install
```

## 46.5 — Verificar

```bash

file /bin/curl
# /bin/curl: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, not stripped


```


---

<< [Anterior](45-nghttp2.md) | [Índice](README.md) | [Siguiente](47-autoconf.md) >>
