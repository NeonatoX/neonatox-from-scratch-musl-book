# Capítulo 23: binutils — Rebuild Nativo (con hash sysv)

## 23.1 — Objetivo

Recompilar binutils 2.46.1 dentro del chroot. El cambio principal
respecto al build anterior es usar `--enable-default-hash-style=sysv`
en vez de `gnu`. El formato `.gnu.hash` no es completamente compatible
con musl y puede causar segfaults en binarios enlazados
dinámicamente.

musl usa el hash style `sysv` (el clásico de ELF), que es más lento
pero 100% fiable. Binutils 2.46.1 tiene `gnu` como default; forzamos
`sysv` para toda la toolchain.

## 23.2 — Compilar

```
cd /sources
rm -rf binutils-2.46.1
tar -xf binutils-2.46.1.tar.xz
cd binutils-2.46.1

mkdir binutils-build
cd binutils-build

../configure --prefix=/usr       \
             --sysconfdir=/etc   \
             --enable-ld=default \
             --enable-plugins    \
             --enable-shared     \
             --disable-werror    \
             --enable-64-bit-bfd \
             --enable-new-dtags  \
             --with-system-zlib  \
             --enable-default-hash-style=sysv

make -j$(nproc)
make install
```

## 23.3 — Verificar

```
ld --version
# → GNU ld (GNU Binutils) 2.46.1

as --version
# → GNU assembler (GNU Binutils) 2.46.1

readelf -a /tmp/test | grep -i hash
# → solo .hash (sysv), NO .gnu.hash
```

## 23.4 — Secuencia de rebuild de la toolchain

La reconstrucción de la toolchain sigue este orden debido a dependencias
cruzadas:

1. **binutils — primer pase** (con `--disable-shared`): GCC actual produce
   binarios no-PIE que segfaultan al enlazar shared libs. Para poder
   compilar musl y GCC, binutils debe compilarse **sin** shared libs
   usando `CFLAGS="-fPIE" LDFLAGS="-pie"` y `--disable-shared`.
2. **musl** (capítulo 22): rebuild simple, no depende de PIE.
3. **GCC** `--enable-default-pie` (capítulo 27): una vez instalado, todos
   los ejecutables se compilan como PIE por defecto.
4. **binutils — segundo pase** (con `--enable-shared`): este capítulo.
   Ya con GCC PIE, se rebuild con shared libs y hash sysv.

## 23.5 — Notas

- `--enable-default-hash-style=sysv`: el cambio clave. Sin esto,
  binutils produce `.gnu.hash` por defecto, que musl no gestiona
  correctamente y causa segfaults en ejecutables dinámicos.
- Este flag también evita la necesidad del wrapper `ld-wrap` con
  `--hash-style=sysv` que se usó como workaround para Perl.
