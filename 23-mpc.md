# Capítulo 23: MPC

## 23.1 — Objetivo

Compilar MPC 1.3.1 como biblioteca del sistema. Necesaria para GCC.

## 23.2 — Dependencias

- GMP 6.3.0
- MPFR 4.2.1
- GCC musl nativo en `/usr/bin/gcc`
- make

---

## 23.3 — Compilar

```
cd /sources
rm -rf mpc-1.3.1
tar -xf mpc-1.3.1.tar.gz
cd mpc-1.3.1

./configure --prefix=/usr CFLAGS="-O2 -std=gnu17"

make -j$(nproc)
make install
```

## 23.4 — Verificar

```
ls /usr/lib/libmpc*
```

---

<< [Anterior](22-mpfr.md) | [Índice](README.md) | [Siguiente](24-zlib.md) >>
