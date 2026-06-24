# Capítulo 49: Nghttp2 1.66.0

## 49.1 — Objetivo

An implementation of HTTP/2 and its header compression algorithm, HPACK. Dependency of curl

## 49.2 — Descargar

```
wget https://github.com/nghttp2/nghttp2/releases/download/v1.66.0/nghttp2-1.66.0.tar.xz
```

## 49.3 — Compilar

```
cd /sources
tar xvf nghttp2-1.66.0.tar.xz
cd nghttp2-0.21.5

./configure --prefix=/usr --disable-static --docdir=/usr/share/doc/nghttp2
make -j$(nproc)

make install
```

## 49.4 — Verificar

```

file /lib/nghttp2.so.5.3.5
# /lib/nghttp2.so.5.3.5: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, not stripped


```
