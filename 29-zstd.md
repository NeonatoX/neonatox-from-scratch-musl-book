# Capítulo 29: zstd 1.5.7

## 29.1 — Objetivo

Compilar zstd (Zstandard) como biblioteca compartida y herramienta de línea
de comandos en `/usr`. El zstd existente en `$LFS/tools` es estático; el
sistema final necesita la `.so` para que binarios como `kernel`, `systemd`,
`rpm`, etc. puedan enlazar contra ella.

## 29.2 — Dependencias

- make, gcc (ya en el sistema)

## 29.3 — Descargar

```
wget https://github.com/facebook/zstd/releases/download/v1.5.7/zstd-1.5.7.tar.gz
```

## 29.4 — Compilar

```bash
cd /sources
tar xf zstd-1.5.7.tar.gz
cd zstd-1.5.7

make -j$(nproc)
make prefix=/usr install
```

Esto instala:
- `/usr/lib/libzstd.so.1.5.7` (shared library)
- `/usr/lib/libzstd.a` (static library)
- `/usr/lib/pkgconfig/libzstd.pc`
- `/usr/include/zstd.h`
- `/usr/bin/zstd` (dinámico contra libzstd.so)
- `/usr/bin/unzstd`, `/usr/bin/zstdcat`, `/usr/bin/zstdmt` (symlinks)

## 29.5 — Verificar

```bash
file /usr/bin/zstd
# → ELF ... dynamically linked

ldd /usr/bin/zstd
# → libzstd.so.1 => /usr/lib/libzstd.so.1
# → libc.so => /lib/ld-musl-x86_64.so.1

echo "foo" | zstd | zstd -d
# → foo

readelf -d /usr/lib/libzstd.so.1.5.7 | grep SONAME
# → 0x000000000000000e (SONAME) Library soname: [libzstd.so.1]
```

## 29.6 — Notas

- `make prefix=/usr install` es obligatorio. Sin `prefix`, Makefile
  instala en `/usr/local` por defecto, y musl no busca ahí a menos que
  se añada `/usr/local/lib` a `/etc/ld-musl-x86_64.path`.
- `make install` crea un symlink `/usr/lib/libzstd.so.1 → libzstd.so.1.5.7`
  y `/usr/lib/libzstd.so → libzstd.so.1`
- Las cabeceras van a `/usr/include/` (zstd.h, zstd_errors.h, zbuff.h,
  dictBuilder/compressStreaming, etc.)
- El paquete incluye `pzstd` (parallel zstd) y `zstdgrep`, `zstdless`,
  `zstdmt` (multi-thread wrapper)
