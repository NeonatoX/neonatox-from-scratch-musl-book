# Capítulo 65: Acl-2.6.0

## 65.1 - Objetivo

Access control list utilities, libraries and headers.

## 65.3 — Descargar

```bash
wget https://download.savannah.gnu.org/releases/acl/acl-2.6.0.tar.gz
```

## 65.4 — Compilar

```bash
./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/acl
make -j$(nproc)


make install

```

## 65.5 — Verificar

```bash
file /usr/lib/libacl.so.1.1.2302
/usr/lib/libacl.so.1.1.2302: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, with debug_info, not stripped
```

---

<< [Anterior](64-attr.md) | [Índice](README.md) | [Siguiente](66-libcap.md) >>
