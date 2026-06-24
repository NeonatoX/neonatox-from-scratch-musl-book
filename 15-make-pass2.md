# Capítulo 15: Make — Pass 2 (nativo musl)

## 15.1 — Objetivo

Compilar make 4.4.1 nativo contra musl dentro del chroot, usando el
make glibc del Pass 1 como bootstrap.


## 15.2 — Descargar

```
wget https://gitlab.alpinelinux.org/alpine/aports/-/raw/master/main/make/getopt-gcc15.patch
wget https://ftpmirror.gnu.org/make/make-4.4.1.tar.gz

```

## 15.3 — Compilar

```
cd /sources
rm -rf make-4.4.1
tar -xf make-4.4.1.tar.gz
cd make-4.4.1

patch -Np1 < ../getopt-gcc15.patch
CFLAGS="-fno-lto" LDFLAGS="-fno-lto" ./configure          --build=$CBUILD                 --host=$CHOST           --prefix=/usr           --mandir=/usr/share/man                 --infodir=/usr/share/info           --disable-nls 


make -j$(nproc) MAKEINFO=true
make install MAKEINFO=true
```

No necesita `--host` ni flags especiales. Dentro del chroot el
compilador y los headers son musl nativos, sin redirecciones C23.

## 15.4 — Verificar

```
file /usr/bin/make
# → ELF ... dynamically linked, interpreter /lib/ld-musl-x86_64.so.1

/usr/bin/make --version
# → GNU Make 4.4.1

ldd /usr/bin/make
# → libc.so → /lib/ld-musl-x86_64.so.1
```

`make install` ya reemplazó el make glibc bootstrap por el nativo musl.
