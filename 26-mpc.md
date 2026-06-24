# Capítulo 26: MPC

## 26.1 — Objetivo

Compilar MPC 1.3.1 como biblioteca del sistema. Necesaria para GCC.

## 26.2 — Compilar

```
cd /sources
rm -rf mpc-1.3.1
tar -xf mpc-1.3.1.tar.gz
cd mpc-1.3.1

./configure --prefix=/usr CFLAGS="-O2 -std=gnu17"

make -j$(nproc)
make install
```

## 26.3 — Verificar

```
ls /usr/lib/libmpc*
```
