# Capítulo 19: file-5.48

## 19.1 — Objetivo

Compilar file 5.48, comando que determina el tipo de archivos binarios.

## 19.2 — Dependencias

- GCC musl nativo en `/usr/bin/gcc`
- make

---

## 19.3 — Compilar

```
cd /sources
rm -rf file-5.48
tar -xf file-5.48.tar.gz
cd file-5.48

./configure --prefix=/usr

make -j$(nproc)
make install
```

## 19.4 — Verificar

```
file /usr/bin/bash
# → ELF ... dynamically linked, interpreter /lib/ld-musl-x86_64.so.1
```

---

<< [Anterior](18-nano.md) | [Índice](README.md) | [Siguiente](20-m4.md) >>
