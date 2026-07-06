# Capítulo 22: MPFR

## 22.1 — Objetivo

Compilar MPFR 4.2.1 como biblioteca del sistema. Necesaria para GCC.

## 22.2 — Dependencias

- GMP 6.3.0 (capítulo anterior)
- GCC musl nativo en `/usr/bin/gcc`
- make

---

## 22.3 — Compilar

```
cd /sources
rm -rf mpfr-4.2.1
tar -xf mpfr-4.2.1.tar.xz
cd mpfr-4.2.1

./configure --prefix=/usr CFLAGS="-O2 -std=gnu17"

make -j$(nproc)
make install
```

## 22.4 — Verificar

```
ls /usr/lib/libmpfr*
```

---

<< [Anterior](21-gmp.md) | [Índice](README.md) | [Siguiente](23-mpc.md) >>
