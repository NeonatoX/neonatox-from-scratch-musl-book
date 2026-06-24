# Capítulo 27: GCC — Rebuild Nativo (con default PIE)

## 27.1 — Objetivo

Recompilar GCC 16.1.0 dentro del chroot con `--enable-default-pie`.
musl requiere que todos los ejecutables sean PIE (Position Independent
Executables). Sin esta opción, GCC produce binarios no-PIE que
segfaultan en musl porque `crt1.o` usa relocaciones `R_X86_64_32`
(32-bit absolutas) que no son compatibles con ejecutables no-PIE
enlazados por binutils moderno.

Requiere GMP, MPFR y MPC instalados como bibliotecas del sistema
(capítulos 24, 25 y 26), y debe compilarse **después** de reconstruir
binutils (capítulo 23) y musl (capítulo 22).

## 27.2 — Compilar

```
cd /sources
rm -rf gcc-16.1.0
tar -xf gcc-16.1.0.tar.xz
cd gcc-16.1.0

mkdir build && cd build

../configure \
  --prefix=/usr \
  --build=x86_64-pc-linux-musl \
  --host=x86_64-linux-musl \
  --target=x86_64-linux-musl \
  --enable-languages=c,c++ \
  --enable-shared \
  --enable-threads=posix \
  --enable-lto \
  --enable-default-pie \
  --disable-multilib \
  --disable-libsanitizer \
  --disable-bootstrap \
  --libdir=/usr/lib

make -j$(nproc)
make install
```

> **Nota sobre libstdc++**: GCC 16.1.0 tiene algunos archivos fuente de
> libstdc++ que usan `throw()` (C++14) y signaturas de
> `basic_string::replace` que no coinciden con las declaraciones C++17
> de los headers. Si `make` falla en `libstdc++-v3`, compila solo GCC
> y omite libstdc++ (ya instalada y funcional del capítulo 09):
> ```
> make -j$(nproc) all-gcc
> make -C gcc install-common
> make install-gcc
> ```

## 27.3 — Verificar

```
gcc --version
# → gcc (GCC) 16.1.0

gcc -dumpmachine
# → x86_64-linux-musl

echo 'int main(){}' | gcc -x c - -o /tmp/test && /tmp/test
file /tmp/test
# → ELF ... pie executable ... interpreter /lib/ld-musl-x86_64.so.1

readelf -h /tmp/test | grep Type
# → DYN (Position Independent Executable)
```

## 27.4 — Configurar path de bibliotecas de musl

musl busca bibliotecas compartidas en los directorios listados en
`/etc/ld-musl-x86_64.path`, no en `/etc/ld.so.conf` como glibc.
Sin este archivo, musl solo busca en `/lib` y `/usr/lib` por defecto,
pero algunos paquetes instalan en subdirectorios.

Para que GCC encuentre `libzstd.so` (y otras) en tiempo de ejecución:

```
echo /usr/lib > /etc/ld-musl-x86_64.path
```

> **Nota**: `cc1` y `cc1plus` se enlazan contra `libzstd.so`. Si la
> biblioteca está en `/usr/local/lib` (por haber compilado zstd sin
> `prefix=/usr`), musl no la encuentra. La solución es copiarla a
> `/usr/lib/` o añadir la ruta al `.path`.

## 27.5 — Enlace simbólico `cc`

Algunos paquetes (como zstd) esperan que exista `cc` como enlace a `gcc`.

```
ln -sfv gcc /usr/bin/cc
```

## 27.6 — Notas

- `--enable-default-pie`: el cambio clave. GCC añade automáticamente
  `-fPIE -pie` a todas las invocaciones, produciendo binarios PIE que
  funcionan correctamente con musl.
- Sin esta opción, los ejecutables dinámicos segfaultan al arrancar
  porque `crt1.o` de musl usa `R_X86_64_32` que el linker resuelve
  incorrectamente en modo no-PIE.
- El workaround manual (pasar `CFLAGS="-fPIE" LDFLAGS="-pie"`) ya no
  es necesario — GCC lo hace automáticamente.
- `--build/host/target` explícitos evitan que configure detecte
  `x86_64-pc-linux-musl` como target, que diferiría del directorio
  existente `/usr/lib/gcc/x86_64-linux-musl/`.
