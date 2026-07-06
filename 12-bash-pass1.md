# Capítulo 12: Bash Pass 1 (musl)

## 12.1 — Objetivo

Compilar bash contra musl para usar como shell interactivo
dentro del chroot y el sistema final.

## 12.2 — Dependencias

- `x86_64-linux-musl-gcc` (cross-compiler o GCC nativo musl)
- Linux API headers en `$LFS/usr/include`

---

## 12.3 — Compilar

```bash
cd $LFS/sources
tar -xf bash-5.3.tar.gz
cd bash-5.3

CC=x86_64-linux-musl-gcc \
./configure \
  --prefix=/usr \
  --without-bash-malloc \
  --target=x86_64-linux-musl
  

make -j$(nproc) bash

make DESTDIR=$LFS install bash

# Symlink /bin/sh → bash
ln -sf bash $LFS/bin/sh
```

### Notas

- `--without-bash-malloc`: usa malloc del sistema (musl) en vez del propio de bash
- El binario resultante es dinámico contra musl, no estático
- Para un bash estático (fallback de emergencia), compilar con `--enable-static-link` y `CFLAGS="-Os -s -static"`

---

## 12.4 — Verificar

```bash
file $LFS/bin/bash
# → ELF ... statically linked, musl

$LFS/bin/bash --version
# → GNU bash, version 5.2.37

# Verificar que no dependa de shared libraries
ldd $LFS/bin/bash 2>&1 | head -3
# → not a dynamic executable
```

---

<< [Anterior](11-make.md) | [Índice](README.md) | [Siguiente](13-archivos-sistema.md) >>
