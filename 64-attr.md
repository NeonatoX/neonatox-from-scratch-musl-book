# Capítulo 64: Attr-2.6.0

## 64.1 - Objetivo

Extended attribute support library for ACL support.

## 64.3 — Descargar

```bash
wget https://download.savannah.gnu.org/releases/attr/attr-2.6.0.tar.gz
```

## 64.4 — Compilar

```bash
./configure --prefix=/usr     \
            --disable-static  \
            --sysconfdir=/etc \
            --docdir=/usr/share/doc/attr
make -j$(nproc)


make install
```

## 64.5 — Verificar

```bash
 file /usr/lib/libattr.so.1.1.2600 
/usr/lib/libattr.so.1.1.2600: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, with debug_info, not stripped

```

---

<< [Anterior](63-libpwquality.md) | [Índice](README.md) | [Siguiente](65-acl.md) >>
