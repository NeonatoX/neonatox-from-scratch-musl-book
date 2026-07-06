# Capítulo 40: Findutils 4.10.0 

## 40.1 — Objetivo

The Findutils package contains programs to find files and is a Nhopkg dependency

## 40.2 — Descargar

```bash
wget https://ftpmirror.gnu.org/findutils/findutils-4.10.0.tar.xz
```

## 40.3 — Compilar

```bash
cd /sources
tar xvf findutils-4.10.0.tar.xz 
cd findutils-4.10.0

./configure --prefix=/usr --localstatedir=/var/lib/locate
make

pkgdir=$PWD/FDEST
make DESTDIR=${pkgdir} install

# Remove locate because we use plocate instead

rm -rfv ${pkgdir}/usr/bin/{locate,updatedb}
rm -rfv ${pkgdir}/usr/libexec/frcode
rm -rfv ${pkgdir}/usr/share/man/man1/{locate,updatedb}.1
rm -rfv ${pkgdir}/usr/share/man/man5/locatedb.5
cp -Rv ${pkgdir}/* /
```

## 40.4 — Verificar

```bash
file /bin/find 
# /bin/find: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, with debug_info, not stripped

```


---

<< [Anterior](39-libarchive.md) | [Índice](README.md) | [Siguiente](41-plocate.md) >>
