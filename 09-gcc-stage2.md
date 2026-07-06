# Capítulo 9: GCC Stage 2 (GCC de transición hacia musl)

## 9.1 — Objetivo

Este capítulo documenta la construcción del **GCC de transición** para
NeonatoX.

Su finalidad no es producir el compilador final del sistema, sino
obtener un GCC completamente funcional (C y C++) instalado en
**$LFS/usr** que permita entrar al chroot y recompilar posteriormente
un GCC totalmente nativo.

## 9.2 — Contexto

### ¿Por qué existe este paso?

Durante el desarrollo del bootstrap se comprobó que el compilador
construido en la primera fase era suficiente para generar la mayor parte
del sistema base, pero no podía recompilarse completamente dentro del
chroot.

La causa estaba relacionada con la configuración `build/host/target` y
con la forma en que GCC construye sus herramientas auxiliares.

### El problema encontrado

Usando:

```
build=x86_64-linux-musl
host=x86_64-linux-musl
target=x86_64-linux-musl
```

mientras todavía se compilaba desde un host glibc, varios programas
auxiliares como:

- collect2
- liblto_plugin.so
- lto-wrapper

quedaban enlazados prematuramente contra musl (`libc.so`), impidiendo su
ejecución correcta durante la propia construcción de GCC.

El síntoma más visible fue:

```bash
error loading plugin:
/usr/lib/libc.so: invalid ELF header
```

### La solución

La configuración que permitió completar la compilación fue:

```bash
build=x86_64-linux-musl
host=x86_64-linux-musl
target=x86_64-linux-musl
```

Con esta combinación:

- las herramientas auxiliares continúan ejecutándose correctamente
  sobre el host glibc;
- el compilador generado produce binarios para musl;
- el sistema queda preparado para entrar al chroot.

---

## 9.3 — Compilar

### Configuración

```sh
PATH=$LFS/tools/bin:/bin:/usr/bin
../configure \
  --prefix=/usr \
  --build=x86_64-pc-linux-gnu \
  --host=x86_64-linux-musl \
  --target=x86_64-linux-musl \
  --with-build-sysroot=$LFS \
  --with-sysroot=$LFS \
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
  CC=x86_64-linux-musl-gcc \
  CXX=x86_64-linux-musl-g++
```

### Explicación de los parámetros

| Parámetro | Motivo |
|-----------|--------|
| `--build=x86_64-pc-linux-gnu` | El sistema que ejecuta la compilación continúa siendo glibc |
| `--host=x86_64-linux-musl` | El compilador instalado será para musl |
| `--target=x86_64-linux-musl` | Genera código para musl |
| `--with-build-sysroot=$LFS` | Usa el entorno de bootstrap durante la construcción |
| `--with-sysroot=$LFS` | Busca cabeceras y bibliotecas del nuevo sistema |
| `--disable-bootstrap` | Reduce tiempo de compilación durante la transición |
| `--disable-multilib` | Construcción únicamente de 64 bits |
| `--libdir=/usr/lib` | Mantiene el esquema habitual utilizado por musl |

### Compilación

```sh
make -j$(nproc)
```

### Instalación

```sh
make DESTDIR=$LFS install
```

## 9.4 — Verificar

### GCC

```sh
$LFS/usr/bin/gcc -v
$LFS/usr/bin/g++ -v
$LFS/usr/bin/gcc -dumpmachine
```

Debe mostrar:

```bash
x86_64-linux-musl
```

### Componentes internos

```sh
file $LFS/usr/libexec/gcc/x86_64-linux-musl/*/cc1
file $LFS/usr/libexec/gcc/x86_64-linux-musl/*/cc1plus
```

Resultado esperado:

```bash
interpreter /lib/ld-musl-x86_64.so.1
```

### Bibliotecas

```sh
readelf -d $LFS/usr/lib/libstdc++.so.6 | grep NEEDED
```

Debe depender de:

```bash
libc.so
libgcc_s.so.1
```

---

## 9.5 — Wrappers temporales

### ¿Por qué son necesarios?

El compilador construido en este capítulo tiene un objetivo muy concreto: permitir finalizar el bootstrap y acceder al primer entorno `chroot`.

Durante la construcción fue necesario configurar GCC con:

```sh
--with-build-sysroot=$LFS
--with-sysroot=$LFS
```

El parámetro `--with-build-sysroot` indica dónde se encuentra el sistema que se está construyendo y únicamente se utiliza durante la compilación.

Sin embargo, `--with-sysroot` queda incorporado en el propio compilador. Como consecuencia, el GCC instalado utiliza por defecto el árbol `$LFS` como raíz del sistema.

Mientras el bootstrap continúa, este comportamiento es correcto, ya que todas las bibliotecas y cabeceras residen precisamente en `$LFS`.

Una vez dentro del `chroot`, la raíz del sistema pasa a ser simplemente `/`. No obstante, el compilador de transición continúa utilizando el sysroot con el que fue construido.

Esto provoca que GCC no encuentre automáticamente las cabeceras estándar del sistema, generando errores similares a:

```bash
fatal error: wchar.h: No such file or directory
```

o mostrando una salida como:

```bash
ignoring nonexistent directory ".../usr/include"
```

### Solución temporal

Hasta recompilar el GCC completamente nativo (capítulo 15), se recomienda utilizar wrappers que añadan explícitamente el sysroot correcto.

```
mkdir -p $LFS/usr/local/bin
```

#### Wrapper para gcc

```sh
cat > $LFS/usr/local/bin/gcc << "EOF"
#!/bin/sh
exec /usr/bin/x86_64-linux-musl-gcc --sysroot=/ "$@"
EOF

chmod 755 /usr/local/bin/gcc
```

#### Wrapper para g++

```sh
cat > $LFS/usr/local/bin/g++ << "EOF"
#!/bin/sh
exec /usr/bin/x86_64-linux-musl-g++ --sysroot=/ "$@"
EOF

chmod 755 /usr/local/bin/g++
```

Comprobar que los wrappers tienen prioridad en el `PATH`:

```sh
which gcc
which g++
```

El resultado debe ser:

```bash
/usr/local/bin/gcc
/usr/local/bin/g++
```

### Carácter temporal

Estos wrappers únicamente son necesarios durante la transición.

En el capítulo **15 - GCC Final**, el compilador se recompilará completamente desde el propio sistema musl utilizando:

```bash
build=x86_64-linux-musl
host=x86_64-linux-musl
target=x86_64-linux-musl
--with-sysroot=/
```

Ese compilador ya utilizará el sistema raíz correctamente y los wrappers podrán eliminarse.

### Nota técnica

Los wrappers no modifican el compilador ni cambian el código generado.

Únicamente fuerzan el uso del sysroot correcto (`/`) mientras el compilador de transición sigue conservando el sysroot del entorno de bootstrap.

Por este motivo constituyen una solución segura y temporal hasta disponer del primer GCC completamente autohospedado.

---

## 9.6 — Pruebas

### Prueba en C

```sh
echo 'int main(){return 0;}' > test.c
$LFS/usr/bin/gcc test.c -o test
```

### Prueba en C++

```sh
cat > test.cpp << EOF
#include <iostream>

int main()
{
    std::cout << "OK\n";
}
EOF

$LFS/usr/bin/g++ test.cpp -o testcpp
```

---

## 9.7 — Notas adicionales

### Flujo completo del bootstrap

```bash
Binutils
      ↓
GCC Cross
      ↓
Sistema base
      ↓
GCC Stage 2 (este capítulo)
      ↓
chroot
      ↓
GCC completamente nativo
```

### Conclusión

Este GCC constituye el compilador de transición de NeonatoX.

Su función es proporcionar un entorno estable para acceder al chroot y
realizar la recompilación final del toolchain completamente nativo,
donde la configuración pasará a ser:

```
build=x86_64-linux-musl
host=x86_64-linux-musl
target=x86_64-linux-musl
```

---

<< [Anterior](08-musl-stage2.md) | [Índice](README.md) | [Siguiente](10-busybox.md) >>
