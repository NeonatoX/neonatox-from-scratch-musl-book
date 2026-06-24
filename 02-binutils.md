# Capítulo 2: Linux API Headers + Binutils 2.44

## 2.1 — Objetivo

Instalar los headers del kernel (para que musl y GCC sepan cómo hablar con
Linux) y compilar binutils (as, ld, ar, etc.) como primer componente de la
toolchain temporal.

## 2.2 — Dependencias

- musl-cross en `$LFS/cross/bin/`
- Nada más (binutils no necesita una libc para compilarse)

---

## 2.3 — Linux API Headers

```
cd $LFS/sources
tar -xf linux-7.1.tar.xz
cd linux-7.1
make mrproper
make ARCH=x86_64 headers
find usr/include -type f ! -name '*.h' -delete
cp -rv usr/include $LFS/tools/usr
cd ..
rm -rf linux-7.1
```

Verificar:

```
ls $LFS/tools/include/linux/kernel.h
# debe existir
```

---

## 2.4 — Compilar binutils

```
cd $LFS/build
tar -xf $LFS/sources/binutils-2.46.1.tar.xz
cd binutils-2.46.1

mkdir build && cd build

../configure \
  --prefix=$LFS/tools \
  --target=x86_64-linux-musl \
  --with-sysroot=$LFS/tools \
  --disable-nls \
  --enable-gprofng=no \
  --disable-werror

make -j$(nproc)
make install
```

### Verificar

```
$LFS/tools/bin/x86_64-linux-musl-ld --version
# → GNU ld (GNU Binutils) 2.46.1
```

---

## 2.5 — Receta nhoid: binutils

```nhoid
#%NHO-0.5
# Package Maintainer:	cargabsj175 <cargabsj175@gmail.com>

# Name:	binutils
# Version:	2.46.1
# Release:	n2026
# License:	GPL-3.0-only
# Repository:	core
# Arch:	x86_64
# Url:	https://sourceware.org/binutils/
# Description:	GNU binutils (stage 1, cross-compiled)
# Packageurl:	https://sourceware.org/pub/binutils/releases/binutils-2.46.1.tar.xz
# SHA256:	<pendiente>

# BuildDep:	linux-api-headers

nbuild() {
mkdir -p build && cd build
../configure \
  --prefix=$LFS/tools \
  --target=x86_64-linux-musl \
  --with-sysroot=$LFS/tools \
  --disable-nls \
  --enable-gprofng=no \
  --disable-werror
make -j$(nproc)
}

ninstall() {
cd build && make install
}

npostinstall() {
noemptyfuncs
}

npostremove() {
noemptyfuncs
}
```

---

## Siguiente paso

[03-gcc-minimal.md](03-gcc-minimal.md) — GCC 14.2.0 pass 1 (solo C, estático).
