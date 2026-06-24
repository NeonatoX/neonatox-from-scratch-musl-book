# Capítulo 61: Gperf 3.3

## 61.1 — Objetivo

Gperf generates a perfect hash function from a key set. 
   
## 61.2 — Descargar

```
wget https://ftpmirror.gnu.org/gperf/gperf-3.3.tar.gz

```

## 61.3 — Compilar

```
cd /sources
tar xvf gperf-3.3.tar.gz
cd gperf-3.3

./configure --prefix=/usr --docdir=/usr/share/doc/gperf-3.3
make

make install

```

## 61.4 — Verificar

```
gperf --version
# GNU gperf 3.3
# Copyright (C) 2025 Free Software Foundation, Inc.

file /usr/bin/gperf          
# /usr/bin/gperf: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, with debug_info, not stripped
```
