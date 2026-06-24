# Capítulo 63: Coreutils 9.11

## 63.1 — Objetivo

The Coreutils package contains the basic utility programs needed by every operating system. 

> Required to build systemd
    
## 63.2 — Descargar

```
wget https://www.linuxfromscratch.org/patches/lfs/development/coreutils-9.11-i18n-1.patch
wget https://ftpmirror.gnu.org/coreutils/coreutils-9.11.tar.xz
```

## 63.3 — Compilar

```
cd /sources
tar xvf coreutils-9.11.tar.xz
cd coreutils-9.11

patch -Np1 -i ../coreutils-9.11-i18n-1.patch
autoreconf -fv
automake -af
FORCE_UNSAFE_CONFIGURE=1 ./configure \
            --prefix=/usr
make

make install
mv -v /usr/share/man/man1/chroot.1 /usr/share/man/man8/chroot.8
sed -i 's/"1"/"8"/' /usr/share/man/man8/chroot.8

```

## 63.4 — Verificar

```
coreutils --version
# GNU coreutils 9.11
# Copyright (C) 2025 Free Software Foundation, Inc.

file /usr/bin/coreutils          
# /usr/bin/coreutils: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, with debug_info, not stripped
```
