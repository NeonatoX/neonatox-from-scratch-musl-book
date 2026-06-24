# Capítulo 13: Make — Pass 1 (host → musl nativo)

## 13.1 — Objetivo

Compilar un make dinámico contra musl desde el host, usando el GCC stage 2
con `--sysroot=/mnt`. Este make servirá como bootstrap dentro del chroot
para el Pass 2.

## 13.2 — Parchear gnulib

GNU make incluye gnulib con algunas declaraciones de función en estilo K&R
(`extern char *getenv ();`) que conflictúan con musl (que espera
`getenv(const char *)`). Hay que parchearlas:

```
cd $LFS/sources
rm -rf make-4.4.1
tar -xf make-4.4.1.tar.gz
cd make-4.4.1

sed -i 's/extern char \*getenv ();/extern char *getenv (const char *);/' \
  lib/fnmatch.c src/getopt.c

sed -i 's/extern int getopt ();/extern int getopt (int, char *const *, const char *);/' \
  src/getopt.h
```

## 13.3 — Compilar

```
PATH=/mnt/usr/bin:/usr/bin:/bin \
CFLAGS="--sysroot=/mnt -fpermissive" \
./configure --prefix=/usr --without-guile

PATH=/mnt/usr/bin:/usr/bin:/bin \
make -j$(nproc) MAKEINFO=true CFLAGS="--sysroot=/mnt -fpermissive"

PATH=/mnt/usr/bin:/usr/bin:/bin \
make DESTDIR=$LFS install MAKEINFO=true
```

## 13.4 — Verificar

```
file $LFS/usr/bin/make
# → ELF ... dynamically linked, interpreter /lib/ld-musl-x86_64.so.1

$LFS/usr/bin/make --version
# → GNU Make 4.4.1
```
