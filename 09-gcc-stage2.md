# Capítulo 9: GCC 16.1.0 (Stage 2 — $LFS/usr, musl nativo)

## 9.1 — Objetivo

Recompilar GCC completo (C, C++, shared, threads) ahora contra musl en
`$LFS/usr`. Este GCC será musl nativo — el compilador final del sistema.

Compilación canadiense: `--build=x86_64-pc-linux-gnu`
→ `--host=x86_64-linux-musl` (corre en musl).

## 9.2 — Dependencias

- `x86_64-linux-musl-gcc` en `$LFS/tools/bin/`
- Binutils musl nativos en `$LFS/usr/bin/`
- musl (shared + static) en `$LFS/usr/lib/`
- Linux API headers en `$LFS/usr/include`
- GMP, MPFR, MPC (mismos que stage 1)

---

## 9.3 — Compilar

```
cd $LFS/sources

rm -rf gcc-16.1.0
tar -xf gcc-16.1.0.tar.xz
cd gcc-16.1.0

# GMP, MPFR, MPC
tar -xf $LFS/sources/gmp-6.3.0.tar.xz
mv gmp-6.3.0 gmp
tar -xf $LFS/sources/mpfr-4.2.1.tar.xz
mv mpfr-4.2.1 mpfr
tar -xf $LFS/sources/mpc-1.3.1.tar.gz
mv mpc-1.3.1 mpc

grep -q '#include <stdbool.h>' gcc/tsystem.h || sed -i '/#include <float.h>/a #include <stdbool.h>' gcc/tsystem.h
sed -i 's/bool preserve_none_abi/int preserve_none_abi/g' gcc/config/i386/i386.h
sed -i 's/label: \n  struct btree_node \*iter;/struct btree_node \*iter;\nlabel:/g' libgcc/unwind-dw2-btree.h 2>/dev/null || \
sed -i 's/case .*:\n  struct btree_node \*iter;/struct btree_node \*iter;\ncase .*: /g' libgcc/unwind-dw2-btree.h
sed -i 's/enable_shadow_stack = 1/enable_shadow_stack = 0/g' gcc/config/i386/i386.h 2>/dev/null

mkdir build && cd build

export PATH="$LFS/tools/bin:$LFS/cross/bin:/usr/bin:/bin"
TGT_CFLAGS="-Dbool=_Bool -Dtrue=1 -Dfalse=0 -D__LIBGCC_SF_MAX__=3.402823466e+38 -D__LIBGCC_SF_MIN__=1.175494351e-38 -D__LIBGCC_SF_EPSILON__=1.1920929e-7 -D__LIBGCC_DF_MAX__=1.7976931348623157e+308 -D__LIBGCC_DF_MIN__=2.2250738585072014e-308 -D__LIBGCC_DF_EPSILON__=2.2204460492503131e-16 -D__LIBGCC_XF_MAX__=1.1897314953573590e+4932 -D__LIBGCC_XF_MIN__=3.3621031782037878e-4932 -D__LIBGCC_XF_EPSILON__=1.9950665184560007e-324"

CFLAGS_FOR_BUILD="-fPIE" \
CXXFLAGS_FOR_BUILD="-fPIE" \
../configure \
  --prefix=/usr \
  --build=x86_64-pc-linux-gnu \
  --host=x86_64-linux-musl \
  --target=x86_64-linux-musl \
  --with-sysroot=/ \
  --with-build-sysroot=$LFS \
  --disable-nls \
  --enable-languages=c,c++ \
  --enable-shared \
  --enable-threads=posix \
  --enable-lto \
  --enable-__cxa_atexit \
  --disable-libsanitizer \
  --disable-libvtv \
  --disable-multilib \
   --disable-libbid \
   --libdir=/usr/lib \
   CC="$LFS/tools/bin/gcc" \
   CXX="$LFS/tools/bin/g++" \
   AR="/usr/bin/ar" \
   NM="/usr/bin/nm" \
   RANLIB="/usr/bin/ranlib" \
   CFLAGS_FOR_TARGET="$TGT_CFLAGS -fno-common"

make -j$(nproc) 2>&1 | tee build.log

# Nota: -Dbool=_Bool funciona para C pero rompe C++ (bool es keyword nativa).
# Por eso se borra libstdc++-v3 y se recompila sin esa definición.
rm -rf $LFS/sources/gcc-16.1.0/build/x86_64-linux-musl/libstdc++-v3
make -j$(nproc) CFLAGS_FOR_TARGET="-D__LIBGCC_SF_MAX__=3.402823466e+38 -D__LIBGCC_SF_MIN__=1.175494351e-38 -D__LIBGCC_SF_EPSILON__=1.1920929e-7 -D__LIBGCC_DF_MAX__=1.7976931348623157e+308 -D__LIBGCC_DF_MIN__=2.2250738585072014e-308 -D__LIBGCC_DF_EPSILON__=2.2204460492503131e-16 -D__LIBGCC_XF_MAX__=1.1897314953573590e+4932 -D__LIBGCC_XF_MIN__=3.3621031782037878e-4932 -D__LIBGCC_XF_EPSILON__=1.9950665184560007e-324 -fno-common"

make DESTDIR=$LFS install
```

### Explicación

| Flag | Significado |
|------|-------------|
| `--prefix=/usr` | Ruta en el sistema final |
| `--host=x86_64-linux-musl` | GCC corre en musl |
| `--with-sysroot=/` | Busca headers/libs en `$LFS` (sysroot) |
| `CC=x86_64-linux-musl-gcc` | Cross-compiler build→host |

`--libdir=/usr/lib` es crítico: GCC por defecto instala bibliotecas
de 64 bits en `/usr/lib64`, pero musl no busca en ese directorio
(el loader solo busca en `/lib`, `/usr/lib` y rutas en
`/etc/ld-musl-x86_64.path`). Sin esta opción, los binarios C++
dinámicos (y libgcc_s) fallan en tiempo de ejecución.

Nota: a diferencia de stage 1, aquí no usamos `--disable-bootstrap`
porque queremos que GCC se auto-compile 3 veces para garantizar que
sea estable. Si el tiempo de compilación es crítico, se puede agregar.

---

## 9.4 — Verificar

```
file $LFS/usr/bin/gcc
# → ELF ... interpreter /lib/ld-musl-x86_64.so.1, musl

$LFS/usr/bin/gcc --version
# → gcc (GCC) 16.1.0

echo 'int main(){return 0;}' | $LFS/usr/bin/gcc -x c - -o /tmp/test3
file /tmp/test3
# → ELF ... interpreter /lib/ld-musl-x86_64.so.1, musl
```

> **Corrección para builds existentes**: si ya compilaste sin `--libdir=/usr/lib`
> y las bibliotecas están en `/usr/lib64`, tienes dos opciones:
> 1. Crear `/etc/ld-musl-x86_64.path` con el contenido `/usr/lib64`
>    (musl lee ese archivo al arrancar y agrega las rutas a su búsqueda).
> 2. Mover/symlinkear cada `.so` de `/usr/lib64` a `/usr/lib`.
>
> La opción 1 es la más limpia si planeas rebuildear GCC más tarde con `--libdir=/usr/lib`.
> Sin alguna de estas correcciones, `g++` —y cualquier binario C++ dinámico—
> falla con *"Error loading shared library libstdc++.so.6"* dentro del chroot.
