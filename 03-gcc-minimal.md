# Capítulo 3: GCC 16.1.0 Pass 1 (mínimo, C estático)

## 3.1 — Objetivo

Compilar un GCC mínimo (solo lenguaje C, estático, sin threads) que sirva
exclusivamente para compilar musl. Este GCC no produce binarios dinámicos
ni tiene C++. Es una herramienta temporal.

## 3.2 — Dependencias

- Binutils 2.46.1 en `$LFS/tools`
- Linux API Headers en `$LFS/tools`

### 3.2.1 — Dependencias extra de GCC (GMP, MPFR, MPC)

GCC necesita las bibliotecas GMP, MPFR y MPC. Las incluimos dentro del árbol
de fuentes de GCC para que se compilen automáticamente:

```
cd $LFS/sources

# GMP
wget https://gmplib.org/download/gmp/gmp-6.3.0.tar.xz

# MPFR
wget https://ftp.gnu.org/gnu/mpfr/mpfr-4.2.1.tar.xz

# MPC
wget https://ftp.gnu.org/gnu/mpc/mpc-1.3.1.tar.gz
```

---

## 3.3 — Preparar fuentes

```
cd $LFS/build
tar -xf $LFS/sources/gcc-16.1.0.tar.xz
cd gcc-16.1.0

# GMP, MPFR, MPC dentro del árbol
tar -xf $LFS/sources/gmp-6.3.0.tar.xz
mv gmp-6.3.0 gmp

tar -xf $LFS/sources/mpfr-4.2.1.tar.xz
mv mpfr-4.2.1 mpfr

tar -xf $LFS/sources/mpc-1.3.1.tar.gz
mv mpc-1.3.1 mpc
```

---

## 3.4 — Preparar headers donde GCC los espera

GCC con `--target=x86_64-linux-musl --with-sysroot=$LFS/tools` busca headers
en `$LFS/tools/x86_64-linux-musl/include/`. Hay que copiar los kernel headers
ahí:

```
mkdir -p $LFS/tools/x86_64-linux-musl/include
cp -r $LFS/tools/usr/include/* $LFS/tools/x86_64-linux-musl/include/
```

## 3.5 — Compilar GCC pass 1

```
cd $LFS/build/gcc-16.1.0
rm -rf build
mkdir build && cd build

../configure \
  --prefix=$LFS/tools \
  --build=$(../config.guess) \
  --host=$(../config.guess) \
  --target=x86_64-linux-musl \
  --with-sysroot=$LFS/tools \
  --without-headers \
  --with-newlib \
  --disable-nls \
  --enable-languages=c \
  --disable-shared \
  --disable-threads \
  --disable-libatomic \
  --disable-libgomp \
  --disable-libquadmath \
  --disable-libssp \
  --disable-libvtv \
  --disable-libstdcxx

make -j$(nproc)
make install
```

### Explicación de flags

| Flag | Significado |
|------|-------------|
| `--build=$(../config.guess)` | Máquina que HACE la compilación (host con glibc) |
| `--host=$(../config.guess)` | Máquina que CORRE el compilador (host, no musl) |
| `--target=x86_64-linux-musl` | Máquina para la que GENERA código (musl) |
| `--without-headers` | No necesita headers del target (musl no existe aún) |
| `--with-newlib` | Desactiva partes de libgcc que necesitan una libc completa |
| `--enable-languages=c` | Solo C, nada de C++ aún |
| `--disable-shared` | Estático (no necesita libc.so todavía) |
| `--disable-threads` | Sin hilos (musl no está listo aún) |
| `--disable-lib*` | Desactiva todas las librerías extra |

### Verificar

```
$LFS/tools/bin/x86_64-linux-musl-gcc --version
# → gcc (GCC) 16.1.0

# Debe ser un binario estático (no depende de nadie)
ldd $LFS/tools/bin/x86_64-linux-musl-gcc
# → "not a dynamic executable" o solo linux-vdso
```

---

## 3.6 — Propósito de este GCC

Este GCC es **mínimo y estático**. Solo sirve para:

1. Compilar musl 1.2.6 (siguiente capítulo)
2. Nada más

No produce binarios dinámicos. No tiene C++. No tiene threads. Es una
herramienta descartable que se reemplazará después.

---

## 3.7 — Receta nhoid: gcc-pass1

```nhoid
#%NHO-0.5
# Package Maintainer:	cargabsj175 <cargabsj175@gmail.com>

# Name:	gcc-pass1
# Version:	16.1.0
# Release:	n2026
# License:	GPL-3.0-only
# Repository:	core
# Arch:	x86_64
# Url:	https://gcc.gnu.org/
# Description:	GCC pass 1 — minimal C static compiler for bootstrapping musl
# Packageurl:	https://ftpmirror.gnu.org/gcc/gcc-16.1.0/gcc-16.1.0.tar.xz
# SHA256:	<pendiente>

# BuildDep:	binutils gmp mpfr mpc

nbuild() {
tar -xf gmp-6.3.0.tar.xz && mv gmp-6.3.0 gmp
tar -xf mpfr-4.2.1.tar.xz && mv mpfr-4.2.1 mpfr
tar -xf mpc-1.3.1.tar.gz && mv mpc-1.3.1 mpc
mkdir build && cd build
mkdir -p $LFS/tools/x86_64-linux-musl/include
cp -r $LFS/tools/usr/include/* $LFS/tools/x86_64-linux-musl/include/
mkdir build && cd build
../configure \
  --prefix=$LFS/tools \
  --build=$(../config.guess) \
  --host=$(../config.guess) \
  --target=x86_64-linux-musl \
  --with-sysroot=$LFS/tools \
  --without-headers \
  --with-newlib \
  --disable-nls \
  --enable-languages=c \
  --disable-shared \
  --disable-threads \
  --disable-libatomic \
  --disable-libgomp \
  --disable-libquadmath \
  --disable-libssp \
  --disable-libvtv \
  --disable-libstdcxx
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

[04-musl-dinamico.md](04-musl-dinamico.md) — musl 1.2.6 (shared library).
