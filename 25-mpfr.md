# Capítulo 25: MPFR

## 25.1 — Objetivo

Compilar MPFR 4.2.1 como biblioteca del sistema. Necesaria para GCC.

## 25.2 — Compilar

```
cd /sources
rm -rf mpfr-4.2.1
tar -xf mpfr-4.2.1.tar.xz
cd mpfr-4.2.1

./configure --prefix=/usr CFLAGS="-O2 -std=gnu17"

make -j$(nproc)
make install
```

## 25.3 — Verificar

```
ls /usr/lib/libmpfr*
```
