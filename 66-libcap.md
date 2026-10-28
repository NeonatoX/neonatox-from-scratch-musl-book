# Capítulo 66: libcap-2.78

## 66.1 - Objetivo

POSIX 1003.1e capabilities.

## 66.3 — Descargar

```bash
wget https://www.kernel.org/pub/linux/libs/security/linux-privs/libcap2/libcap-2.78.tar.xz
```

## 66.4 — Compilar

```bash
sed -i '/install -m.*STA/d' libcap/Makefile
make -j$(nproc) prefix=/usr lib=lib

make DESTDIR=/usr lib=lib install
```


## 66.5 — Verificar

```bash
file /usr/lib/libcap.so.2.78 
/usr/lib/libcap.so.2.78: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-x86_64.so.1, with debug_info, not stripped

```


---

<< [Anterior](65-acl.md) | [Índice](README.md) | [Siguiente](67-shadow.md) >>
