# Capítulo 4: musl 1.2.6 (dinámico)

## 4.1 — Objetivo

Compilar musl como librería compartida (`libc.so`) usando el GCC pass 1.
musl es la primera pieza del sistema que es completamente nativa (no cruzada).

## 4.2 — Dependencias

- GCC pass 1 en `$LFS/tools` (capítulo anterior)
- Binutils 2.46.1 en `$LFS/tools`
- Linux API Headers en `$LFS/tools`

---

## 4.3 — Compilar musl

```
cd $LFS/build
rm -rf musl-1.2.6  # limpiar por si existe
tar -xf $LFS/sources/musl-1.2.6.tar.gz
cd musl-1.2.6

./configure \
  CROSS_COMPILE=x86_64-linux-musl- \
  --prefix=$LFS/tools \
  --target=x86_64-linux-musl \
  --enable-shared \
  --disable-static

make -j$(nproc)
make install
```

### Explicación de flags

| Flag | Significado |
|------|-------------|
| `CROSS_COMPILE=x86_64-linux-musl-` | Usa el GCC pass 1 como compilador |
| `--prefix=$LFS/tools` | Toolchain temporal |
| `--enable-shared` | `libc.so` dinámica |
| `--disable-static` | No compila `libc.a` (innecesario) |

### Verificar

```
$LFS/tools/lib/libc.so
# Debe ser un ejecutable musl nativo (sin interpreter glibc)

$LFS/tools/lib/libc.so
# Debe imprimir versión → "musl libc (x86_64) ... release 1.2.6"
```

---

## 4.4 — ¿Qué cambió?

Ahora tenemos `$LFS/tools/lib/libc.so` — la primera librería 100% musl.
El GCC pass 1 que usamos para compilarla es estático (no necesita libc).
Pero musl SÍ es dinámica: cualquier programa que enlace contra ella será
un binario musl.

---

## 4.5 — Receta nhoid: musl

```nhoid
#%NHO-0.5
# Package Maintainer:	cargabsj175 <cargabsj175@gmail.com>

# Name:	musl
# Version:	1.2.6
# Release:	n2026
# License:	MIT
# Repository:	core
# Arch:	x86_64
# Url:	https://musl.libc.org/
# Description:	musl libc — shared library (stage 1 toolchain)
# Packageurl:	https://musl.libc.org/releases/musl-1.2.6.tar.gz
# SHA256:	<pendiente>

# BuildDep:	gcc-pass1 binutils linux-api-headers

nbuild() {
./configure \
  CROSS_COMPILE=x86_64-linux-musl- \
  --prefix=$LFS/tools \
  --target=x86_64-linux-musl \
  --enable-shared \
  --disable-static
make -j$(nproc)
}

ninstall() {
make install
}

npostinstall() {
noemptyfuncs
}

npostremove() {
noemptyfuncs
}
```

---

## Siguiente paso

[05-gcc-completo.md](05-gcc-completo.md) — GCC 16.1.0 completo (C++, shared,
threads) contra musl.
