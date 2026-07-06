# Capítulo 11: Make (host → musl nativo)

## 11.1 — Objetivo

Compilar un make dinámico contra musl desde el host, usando el GCC stage 1
con `--sysroot=$LFS`. Este make servirá como bootstrap dentro del chroot
para el Pass 2.

## 11.2 — Parchear gnulib

GNU make incluye gnulib con algunas declaraciones de función en estilo K&R
(`extern char *getenv ();`) que conflictúan con musl (que espera
`getenv(const char *)`). Hay que parchearlas:

```bash
cd $LFS/sources
rm -rf make-4.4.1
tar -xf make-4.4.1.tar.gz
cd make-4.4.1

sed -i 's/extern char \*getenv ();/extern char *getenv (const char *);/' \
  lib/fnmatch.c src/getopt.c

sed -i 's/extern int getopt ();/extern int getopt (int, char *const *, const char *);/' \
  src/getopt.h

```

## 11.3 — Compilar

```bash
CC=x86_64-linux-musl-gcc \
./configure \
  --prefix=/usr \
  --target=x86_64-linux-musl
  
make -j$(nproc)

make DESTDIR=$LFS install
```

## 11.4 — Verificar

```bash
file $LFS/usr/bin/make
# → ELF ... dynamically linked, interpreter /lib/ld-musl-x86_64.so.1

$LFS/usr/bin/make --version
# → GNU Make 4.4.1
```

---

<< [Anterior](10-busybox.md) | [Índice](README.md) | [Siguiente](12-bash-pass1.md) >>
