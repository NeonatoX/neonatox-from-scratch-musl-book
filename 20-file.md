# Capítulo 20: file

## 20.1 — Objetivo

Compilar file 5.48, comando que determina el tipo de archivos binarios.

## 20.2 — Compilar

```
cd /sources
rm -rf file-5.48
tar -xf file-5.48.tar.gz
cd file-5.48

./configure --prefix=/usr

make -j$(nproc)
make install
```

## 20.3 — Verificar

```
file /usr/bin/bash
# → ELF ... dynamically linked, interpreter /lib/ld-musl-x86_64.so.1
```
