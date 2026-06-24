# Capítulo 28: libstdc++ — Rebuild independiente

## 28.1 — Objetivo

Recompilar solo libstdc++ (la biblioteca C++ de GCC) de forma independiente,
sin necesidad de reconstruir todo GCC.

## 28.2 — Por qué aparte

GCC 16.1.0 configurado por defecto compila libstdc++ en modo C++17, pero el
código fuente de libstdc++ 16.1.0 tiene fragmentos legacy que no son
compatibles con C++17. Estos errores solo aparecen al compilar GCC 16.1.0
**con GCC 16.1.0** (el rebuild nativo), porque el GCC nuevo por defecto usa
C++17. En el stage 2 (capítulo 09), que usaba musl-cross 9.4.0 (GCC 9,
C++14 por defecto), estos errores no ocurrían.

El sistema de build de GCC (`make bootstrap`) falla en `stage1-bubble` si
libstdc++ no compila, impidiendo completar el rebuild. Compilar libstdc++
por separado evita este bloqueo.

## 28.3 — Problemas encontrados

Al compilar con `CXXFLAGS="-std=gnu++17 -fpermissive"`, tres archivos de
`src/c++98` fallan porque los headers de GCC 16 en modo C++17 ya no exponen
las APIs pre-C++11 que las instanciaciones explícitas esperan:

1. **`bitmap_allocator.cc`**: usa `throw(std::bad_alloc)` (especificación
   dinámica eliminada en C++17)
2. **`ios_locale.cc`**: instancia `operator void*` (conversión eliminada
   en C++17)
3. **`misc-inst.cc`**: instancia `replace(iterator, iterator, ...)` e
   `insert(iterator, ...)` (reemplazadas por `const_iterator` en C++17)

Además, forzar `-std=gnu++17` rompe los `#error` guards que verifican el
standard en `misc-inst.cc` (`__cplusplus != 199711L`) y `limits.cc`
(`#error This file must be compiled as C++11 or C++14`), y causa
redefiniciones en `placeholders.cc`.

## 28.4 — Solución

NO pasar `-std=gnu++17`. El build system de libstdc++ ya asigna el standard
correcto por directorio (`-std=gnu++98` para `src/c++98/`, `-std=gnu++11`
para `src/c++11/`). Solo hace falta `-fpermissive` para que ciertos
constructos legacy (como `throw()`) sean warnings en vez de errores.

Sí es necesario parchar los tres archivos C++98 problemáticos porque,
incluso compilando en modo C++98, los headers generados de GCC 16 no tienen
los overloads antiguos con `iterator`:

## 28.5 — Compilar

Primero parchar los tres archivos fuente problemáticos:

```bash
cd /sources/gcc-16.1.0

sed -i 's/ throw(std::bad_alloc)//' libstdc++-v3/src/c++98/bitmap_allocator.cc
sed -i '/template.*operator void\*/,/#endif/d' libstdc++-v3/src/c++98/ios_locale.cc
sed -i '/\/\/ C++98 members/,/^#endif$/d' libstdc++-v3/src/c++98/misc-inst.cc
```

Los tres `sed`:
- `bitmap_allocator.cc`: elimina `throw(std::bad_alloc)` de las firmas
- `ios_locale.cc`: elimina instanciaciones de `operator void*` (desde la
  línea con `template` hasta el `#endif` que las cierra)
- `misc-inst.cc`: elimina TODO el bloque de instanciaciones pre-C++11
  (`erase`, `insert`, `replace` con `iterator`), que no existen en los
  headers de GCC 16 para ningún standard

Luego configurar y compilar:

```bash
cd /sources
rm -rf gcc-16.1.0
tar xf gcc-16.1.0.tar.xz
cd gcc-16.1.0

sed -i 's/ throw(std::bad_alloc)//' libstdc++-v3/src/c++98/bitmap_allocator.cc
sed -i '/template.*operator void\*/,/#endif/d' libstdc++-v3/src/c++98/ios_locale.cc
sed -i '/\/\/ C++98 members/,/^#endif$/d' libstdc++-v3/src/c++98/misc-inst.cc

mkdir -p libstdc++-build
cd libstdc++-build

CXXFLAGS="-fpermissive" \
../libstdc++-v3/configure \
  --prefix=/usr \
  --host=x86_64-linux-musl \
  --build=x86_64-linux-musl \
  --target=x86_64-linux-musl \
  --enable-shared \
  --disable-multilib \
  --disable-nls \
  --with-gxx-include-dir=/usr/include/c++/16.1.0 \
  --disable-libstdcxx-pch \
  --libdir=/usr/lib

make -j$(nproc)
make install
```

> **Importante**: Usar `CXXFLAGS="-fpermissive"` sin `-std=`. El build system
> de libstdc++ ya asigna el standard correcto según el directorio
> (`-std=gnu++98` para `src/c++98/`, `-std=gnu++11` para `src/c++11/`).
> Forzar `-std=gnu++17` rompe los `#error` guards y causa redefiniciones.

## 28.6 — Limpiar lib64 obsoleto

Si existía una versión anterior de libstdc++ en `/usr/lib64`, hay que
reemplazar el symlink para que apunte al nuevo:

```bash
rm -f /usr/lib64/libstdc++.so.6 /usr/lib64/libstdc++.so.6.0.35
ln -s /usr/lib/libstdc++.so.6.0.35 /usr/lib64/libstdc++.so.6
```

## 28.7 — Verificar

```bash
ls -la /usr/lib/libstdc++.so.6
# → ... -> libstdc++.so.6.0.35

strings /usr/lib/libstdc++.so.6 | grep 'GLIBCXX'
# → GLIBCXX_3.4, GLIBCXX_3.4.30, ...

g++ --version
echo 'int main(){}' | g++ -x c++ - -o /tmp/test && /tmp/test
# → sin errores
```
