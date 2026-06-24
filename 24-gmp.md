# Capítulo 24: GMP

## 24.1 — Objetivo

Compilar GMP 6.3.0 como biblioteca del sistema. Necesaria para GCC.

## 24.2 — Compilar

```
cd /sources
rm -rf gmp-6.3.0
tar -xf gmp-6.3.0.tar.xz
cd gmp-6.3.0

./configure --prefix=/usr ABI=64 CFLAGS="-O2 -std=gnu17"

make -j$(nproc)
make install
```

> Nota: se necesita `-std=gnu17` porque GMP usa declaraciones de función
> K&R (paréntesis vacíos) que conflictúan con el estándar C23 por
> defecto de GCC 16.

## 24.3 — Verificar

```
ls /usr/lib/libgmp*
# → /usr/lib/libgmp.so  /usr/lib/libgmp.a
```
