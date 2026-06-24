# Capítulo 34: libffi 3.5.2

## 34.1 — Objetivo

Compilar libffi 3.5.2 en `/usr`. Es necesario para Python (ctypes) y otros
paquetes que necesitan invocar funciones C en tiempo de ejecución.

## 34.2 — Dependencias

- gcc, make (ya en el sistema)

## 34.3 — Descargar

```
wget https://github.com/libffi/libffi/releases/download/v3.5.2/libffi-3.5.2.tar.gz
```

## 34.4 — Compilar

```bash
cd /sources
tar xf libffi-3.5.2.tar.gz
cd libffi-3.5.2

./configure --prefix=/usr --disable-static
make
make install
```

## 34.5 — Verificar

```bash
file /usr/lib/libffi.so.8.2.0
# → ELF 64-bit LSB shared object, x86-64, ... dynamically linked

ldd /usr/lib/libffi.so.8.2.0
# → libc.so => /lib/ld-musl-x86_64.so.1
```

## 34.6 — Notas

- `--disable-static` ahorra espacio; nadie necesita `libffi.a`
- libffi se instala en `/usr/lib/` (no `/usr/lib64/`)
- Python lo necesita para `ctypes` y `_ctypes`
