# Capítulo 51: Findutils 4.10.0 

## 51.1 — Objetivo

The Findutils package contains programs to find files and is a Nhopkg dependency

## 51.2 — Descargar

```
wget https://ftpmirror.gnu.org/findutils/findutils-4.10.0.tar.xz
```

## 51.3 — Compilar

```
cd /sources
tar xvf findutils-4.10.0.tar.xz 
cd findutils-4.10.0

./configure --prefix=/usr --localstatedir=/var/lib/locate
make

make install
```

## 51.4 — Verificar

```
file /bin/file
# /bin/file: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, with debug_info, not stripped

```
