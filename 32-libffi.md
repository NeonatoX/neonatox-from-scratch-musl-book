# Capítulo 32: libffi 3.5.2

## 32.1 — Objetivo

Compilar libffi 3.5.2 en `/usr`. Es necesario para Python (ctypes) y otros
paquetes que necesitan invocar funciones C en tiempo de ejecución.

## 32.2 — Dependencias

- gcc, make (ya en el sistema)

## 32.3 — Descargar

```bash
wget https://github.com/libffi/libffi/releases/download/v3.5.2/libffi-3.5.2.tar.gz
```

## 32.4 — Compilar

```bash
cd /sources
tar xf libffi-3.5.2.tar.gz
cd libffi-3.5.2

./configure --prefix=/usr --disable-static
make -j$(nproc)

make install
```

## 32.5 — Verificar

```bash
file /usr/lib/libffi.so.8.2.0
# → ELF 64-bit LSB shared object, x86-64, ... dynamically linked

ldd /usr/lib/libffi.so.8.2.0
# → libc.so => /lib/ld-musl-x86_64.so.1
```

## 32.6 — Notas

- `--disable-static` ahorra espacio; nadie necesita `libffi.a`
- libffi se instala en `/usr/lib/` (no `/usr/lib64/`)
- Python lo necesita para `ctypes` y `_ctypes`


---

<< [Anterior](31-pkgconf.md) | [Índice](README.md) | [Siguiente](33-flex.md) >>
