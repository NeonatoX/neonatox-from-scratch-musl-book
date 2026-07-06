# Capítulo 27: GCC Final (Compilador completamente nativo)

## 27.1 Objetivo

Este capítulo construye el primer GCC completamente nativo de NeonatoX.

A diferencia del compilador obtenido durante el bootstrap (Capítulo 09), este GCC ya no depende del entorno de construcción inicial ni del árbol $LFS.

El sistema ya se encuentra ejecutándose íntegramente sobre musl, por lo que la compilación se realiza completamente dentro del chroot utilizando el propio sistema construido.

Al finalizar este capítulo, GCC pasa a ser el compilador definitivo del sistema y los wrappers temporales utilizados durante la transición dejan de ser necesarios.


## 26.2 — Dependencias

### Requisitos previos

Antes de compilar GCC final, verifique que los siguientes paquetes ya están instalados:

- make
- gcc (STAGE 2)
- GNU Awk (gawk)
- GNU Sed (sed)
- GMP
- MPFR
- MPC
- zlib

## 27.3 ¿Por qué recompilar GCC?

El GCC construido durante el bootstrap tenía como único objetivo permitir finalizar la construcción del sistema base.

Para conseguirlo fue necesario configurarlo utilizando:

```
--with-build-sysroot=$LFS
--with-sysroot=$LFS

```

Esta configuración permitía utilizar las cabeceras y bibliotecas presentes en el árbol de bootstrap.

Una vez dentro del chroot, la raíz del sistema pasa a ser /, por lo que esas opciones dejan de tener sentido.

Este capítulo elimina definitivamente esa dependencia y produce el primer compilador completamente autohospedado.

## 27.4 Requisitos importantes

Antes de compilar GCC final, asegúrese de que el sistema dispone de GNU gawk y GNU sed. Las implementaciones incluidas en BusyBox no son suficientes para esta etapa.

Durante el desarrollo de NeonatoX se comprobó que GCC podía compilar aparentemente sin errores, pero el cc1plus generado interpretaba incorrectamente las opciones del estándar de C++, provocando fallos posteriores durante la construcción de libstdc++.


## 27.5 Configurar

```
mkdir build
cd build

../configure \
  --prefix=/usr \
  --build=x86_64-linux-musl \
  --host=x86_64-linux-musl \
  --target=x86_64-linux-musl \
  --with-build-sysroot=/ \
  --with-sysroot=/ \
  --enable-default-pie \
  --enable-default-ssp \
  --disable-nls \
  --enable-languages=c,c++ \
  --enable-shared \
  --enable-threads=posix \
  --enable-__cxa_atexit \
  --disable-multilib \
  --disable-bootstrap \
  --libdir=/usr/lib \
  CC=gcc \
  CXX=g++
```

## 27.6 ¿Qué cambia respecto al Stage 2?

Ahora:

build, host y target son idénticos.
Ya no existe ningún componente ejecutándose sobre glibc.
No se utiliza ningún sysroot externo.
GCC utiliza directamente el sistema instalado.

Este es el primer compilador verdaderamente nativo del sistema.

## 27.7 Compilar e instalar

```
make -j$(nproc)
make install

ln -svf x86_64-linux-musl-gcc /usr/bin/cc
```

## 27.8 Eliminar los wrappers temporales

```
rm -f /usr/local/bin/gcc
rm -f /usr/local/bin/g++
rm -f /usr/local/bin/cc
```

## 27.9 Actualizar la caché del shell:

```
hash -r
```

Comprobar:

```
which gcc
which g++
```

Resultado esperado:

```
/usr/bin/gcc
/usr/bin/g++

```

## 27.10 Verificaciones

### Arquitectura

```
gcc -dumpmachine
```
Debe mostrar:

```
x86_64-linux-musl
```

### Sysroot
```
gcc -print-sysroot
```
Ya no debe hacer referencia al antiguo árbol $LFS.

### Componentes internos
```
gcc -print-prog-name=cc1

gcc -print-search-dirs
```
No deben aparecer rutas pertenecientes al entorno de bootstrap.

### Prueba en C
```
#include <stdio.h>

int main(void)
{
    puts("Hello NeonatoX");
    return 0;
}
gcc test.c -o test
./test
```
### Prueba en C++
```
#include <iostream>

int main()
{
    std::cout << "Hello NeonatoX\n";
}
g++ test.cpp -o testcpp
./testcpp
```
### Bibliotecas
```
readelf -d /usr/lib/libstdc++.so.6 | grep NEEDED
```
Verificar que la biblioteca enlaza correctamente con el sistema.

## 27.11 Conclusión

Con este capítulo finaliza la construcción del toolchain de NeonatoX.

A partir de este punto:

GCC es completamente nativo.
El sistema deja de depender del entorno de bootstrap.
Los wrappers temporales pueden eliminarse.
Las compilaciones posteriores utilizarán exclusivamente el toolchain generado por el propio sistema.

---

<< [Anterior](26-sed.md) | [Índice](README.md) | [Siguiente](28-zstd.md) >>
