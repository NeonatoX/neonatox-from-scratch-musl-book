# Capítulo 39: libarchive 3.8.8

## 39.1 — Objetivo

 The libarchive library provides a single interface for reading/writing various compression formats. 

## 39.2 — Descargar

```bash
wget https://github.com/libarchive/libarchive/releases/download/v3.8.8/libarchive-3.8.8.tar.xz

```

## 39.3 — Compilar

```bash
cd /sources
tar xvf libarchive-3.8.8.tar.xz
cd libarchive-3.8.8

./configure --prefix=/usr --disable-static
make

make install

# optional
# ln -sfv bsdunzip /usr/bin/unzip
```

## 39.4 — Verificar

```bash
file /lib/libarchive.so.13.8.8
# /lib/libarchive.so.13.8.8: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, with debug_info, not stripped

```


---

<< [Anterior](38-ninja-meson.md) | [Índice](README.md) | [Siguiente](40-findutils.md) >>
