# Capítulo 7: Binutils 2.46.1 (Stage 2 — $LFS/usr)

## 7.1 — Objetivo

Recompilar binutils para el target `x86_64-linux-musl`, instalándolos en
`$LFS/usr`. Esta vez serán musl nativos (a diferencia de los de stage 1,
que son glibc-linked).

La compilación es cross (canadiense): `--build=x86_64-pc-linux-gnu`
(build host) → `--host=x86_64-linux-musl` (target final).

## 7.2 — Dependencias

- `x86_64-linux-musl-gcc` en `$LFS/tools/bin/`
- Linux API headers en `$LFS/usr/include`

---

## 7.3 — Compilar

```bash
cd $LFS/sources
rm -rf binutils-2.46.1
tar -xf binutils-2.46.1.tar.xz
cd binutils-2.46.1

mkdir build && cd build

CC=x86_64-linux-musl-gcc \
AR=x86_64-linux-musl-ar \
../configure \
  --prefix=/usr \
  --build=$(../config.guess) \
  --host=x86_64-linux-musl \
  --target=x86_64-linux-musl \
  --with-sysroot=/ \
  --disable-nls \
  --disable-multilib

make -j$(nproc) 2>&1 | tee build.log

make DESTDIR=$LFS install

ln -sf ar      $LFS/usr/bin/x86_64-linux-musl-ar
ln -sf ranlib  $LFS/usr/bin/x86_64-linux-musl-ranlib
ln -sf nm      $LFS/usr/bin/x86_64-linux-musl-nm
ln -sf strip   $LFS/usr/bin/x86_64-linux-musl-strip

```



### Explicación

| Flag | Significado |
|------|-------------|
| `--prefix=/usr` | Ruta en el sistema final (DESTDIR=$LFS lo mueve) |
| `--host=x86_64-linux-musl` | Los binarios correrán en musl |
| `--target=x86_64-linux-musl` | Producen código para musl |
| `--with-sysroot=/` | Busca headers/libs en `/` del target |
| `CC=x86_64-linux-musl-gcc` | Cross-compiler build→host |

---

## 7.4 — Verificar

```bash
file $LFS/usr/bin/ld
# → ELF ... interpreter /lib/ld-musl-x86_64.so.1, musl

# También verificar as y ar
file $LFS/usr/bin/as $LFS/usr/bin/ar
# → Ambos musl nativos
```

Si ves `ld-linux-x86-64.so.2`, algo falló (sigue glibc-linked).

---

<< [Anterior](06-linux-headers.md) | [Índice](README.md) | [Siguiente](08-musl-stage2.md) >>
