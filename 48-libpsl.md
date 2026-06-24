# Capítulo 48: Libpsl 0.21.5 

## 48.1 — Objetivo

Public Suffix List library is a curl dependency

## 48.2 — Descargar

```
wget https://github.com/rockdaboot/libpsl/releases/download/0.21.5/libpsl-0.21.5.tar.gz
```

## 48.3 — Compilar

```
cd /sources
tar xvf libpsl-0.21.5.tar.gz 
cd libpsl-0.21.5

mkdir build && cd build

meson setup --prefix=/usr --buildtype=release
ninja

ninja install
```

## 48.4 — Verificar

```

file /lib/libpsl.so.5.3.5
# /lib/libpsl.so.5.3.5: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, not stripped


```
