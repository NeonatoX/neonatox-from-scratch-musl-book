# Capítulo 66: libcap-2.78

## 66.1 - Objetivo

POSIX 1003.1e capabilities.

## 66.3 — Descargar

```bash
wget https://www.kernel.org/pub/linux/libs/security/linux-privs/libcap2/libcap-2.78.tar.xz
```

## 66.4 — Compilar

```bash
./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/libcap
make


make install

```


---

<< [Anterior](65-acl.md) | [Índice](README.md) | [Siguiente](67-shadow.md) >>
