# Capítulo 62: Gettext 1.0

## 62.1 — Objetivo

The Gettext package contains utilities for internationalization and localization. These allow programs to be compiled with NLS (Native Language Support), enabling them to output messages in the user's native language.    

> Optional dependency for SystemD

## 62.2 — Descargar

```
wget https://ftpmirror.gnu.org/gettext/gettext-1.0.tar.xz

```

## 62.3 — Compilar

```
cd /sources
tar xvf gettext-1.0.tar.xz
cd gettext-1.0

./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/gettext-1.0
make

make install
chmod -v 0755 /usr/lib/preloadable_libintl.so

```

## 62.4 — Verificar

```
gettext --version
# GNU gettext 1.0
# Copyright (C) 2025 Free Software Foundation, Inc.

file /usr/bin/gettext          
# /usr/bin/gettext: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, with debug_info, not stripped
```
