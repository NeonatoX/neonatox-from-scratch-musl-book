# Capítulo 18: nano-9.0

> No es que no nos guste vi (aun tienes el de busybox :-) ). nano es el hermanito que todos quieren.

## 18.1 — Objetivo

Compilar nano 9.0, editor de texto amigable para el sistema musl.

## 18.2 — Dependencias

- GCC musl nativo en `/usr/bin/gcc`
- make

---

## 18.3 — Compilar

```bash
cd /sources
rm -rf nano-9.0
tar -xf nano-9.0.tar.xz
cd nano-9.0

./configure --prefix=/usr

make -j$(nproc)
make install
```

## 18.4 — Verificar

```
nano --version
# → GNU nano, version 9.0
```

---

<< [Anterior](17-bash-final.md) | [Índice](README.md) | [Siguiente](19-file.md) >>
