# Capítulo 19: nano

> No es que no nos guste vi. nano es el hermanito que todos quieren.

## 19.1 — Objetivo

Compilar nano 9.0, editor de texto amigable para el sistema musl.

## 19.2 — Compilar

```
cd /sources
rm -rf nano-9.0
tar -xf nano-9.0.tar.xz
cd nano-9.0

./configure --prefix=/usr

make -j$(nproc)
make install
```

## 19.3 — Verificar

```
nano --version
# → GNU nano, version 9.0
```
