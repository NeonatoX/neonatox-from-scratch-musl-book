# Capítulo 11: Bash (Estático musl)

## 11.1 — Objetivo

Compilar bash estático contra musl para usar como shell interactivo
dentro del chroot y el sistema final.

## 11.2 — Dependencias

- `x86_64-linux-musl-gcc` (cross-compiler o GCC nativo musl)
- Linux API headers en `$LFS/usr/include`

---

## 11.3 — Compilar

```
cd $LFS/sources
tar -xf bash-5.3.tar.gz
cd bash-5.3

MUSLGCC=x86_64-linux-musl-gcc

CC="$MUSLGCC" \
CFLAGS="-static -Os -s" \
LDFLAGS="-static" \
./configure \
  --host=x86_64-linux-musl \
  --enable-static-link \
  --without-bash-malloc \
  --disable-largefile \
  --disable-nls \
  --prefix=/

make -j$(nproc) bash

make DESTDIR=$LFS install-bash

# Symlink /bin/sh → bash
ln -sf bash $LFS/bin/sh
```

### Notas

- `--prefix=/`: instala bash en `/bin/bash`, no en `/usr/bin/bash`
- `--enable-static-link`: enlaza musl estáticamente
- `-Os -s -static`: optimiza tamaño y elimina símbolos

---

## 11.4 — Verificar

```
file $LFS/bin/bash
# → ELF ... statically linked, musl

$LFS/bin/bash --version
# → GNU bash, version 5.2.37

# Verificar que no dependa de shared libraries
ldd $LFS/bin/bash 2>&1 | head -3
# → not a dynamic executable
```
