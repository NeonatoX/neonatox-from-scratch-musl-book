# Capítulo 24: zlib 1.3.2

## 24.1 — Objetivo

Compilar zlib como biblioteca compartida en `/usr`. Es dependencia de
openssl, curl, python, ssh, y muchas otras herramientas del sistema.

## 24.2 — Dependencias

- make, gcc (ya en el sistema)

## 24.3 — Descargar

```
wget https://zlib.net/zlib-1.3.2.tar.gz
```

## 24.4 — Compilar

```bash
cd /sources
tar xf zlib-1.3.2.tar.gz
cd zlib-1.3.2

./configure --prefix=/usr --libdir=/usr/lib --shared
make -j$(nproc)
make install
```

Flags:
- `--shared`: compila solo la biblioteca compartida (`.so`)
- `--libdir=/usr/lib`: musl busca en `/usr/lib`, no `/usr/lib64`

## 24.5 — Verificar

```bash
ldconfig /usr/lib
file /usr/lib/libz.so.1.3.2
# → ELF ... shared object

ldd /usr/lib/libz.so.1.3.2
# → libc.so => /lib/ld-musl-x86_64.so.1

echo -n | gzip | zcat > /dev/null
# → sin errores
```

---

<< [Anterior](23-mpc.md) | [Índice](README.md) | [Siguiente](25-gawk.md) >>
