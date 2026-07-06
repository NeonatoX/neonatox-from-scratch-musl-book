# Capítulo 45: Nghttp2 1.66.0

## 45.1 — Objetivo

An implementation of HTTP/2 and its header compression algorithm, HPACK. Dependency of curl

## 45.2 — Descargar

```bash
wget https://github.com/nghttp2/nghttp2/releases/download/v1.66.0/nghttp2-1.66.0.tar.xz
```

## 45.3 — Compilar

```bash
cd /sources
tar xvf nghttp2-1.66.0.tar.xz
cd nghttp2-0.21.5

./configure --prefix=/usr --disable-static --docdir=/usr/share/doc/nghttp2
make -j$(nproc)

make install
```

## 45.4 — Verificar

```bash

file /lib/nghttp2.so.5.3.5
# /lib/nghttp2.so.5.3.5: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, not stripped


```


---

<< [Anterior](44-libpsl.md) | [Índice](README.md) | [Siguiente](46-curl.md) >>
