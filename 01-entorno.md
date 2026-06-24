# Capítulo 1: Preparación del Entorno

## 1.1 — Objetivo

Tener un directorio `$LFS=/mnt` con la estructura base y el cross-compiler
semilla (`musl-cross`) listo para empezar a compilar.

## 1.2 — Concepto clave

musl-cross es un compilador que **corre con glibc** (el idioma de tu PC) pero
**produce binarios musl**. Lo usamos desde el host para compilar los primeros
componentes de la toolchain. Cuando tengamos un compilador nativo musl, haremos
chroot y seguiremos desde ahí.

## 1.3 — Requisitos

- Tu sistema normal (el que tiene `/opt/cross-x86_64-linux-musl/`)
- Un disco extra (10 GB mínimo)
- Conexión a internet (para descargar tarballs)
- `gcc`, `make`, `wget`, `tar` — herramientas básicas del host

---

## 1.4 — Preparar el disco destino

Identificar el disco (`lsblk`) y particionar:

```
# fdisk /dev/sdX
```

Crear una partición primaria tipo Linux.

Formatear como ext4:

```
# mkfs.ext4 /dev/sdX1
```

Montar:

```
# mount /dev/sdX1 /mnt
```

---

## 1.5 — Crear estructura LFS

Estructura completa del sistema (NeonatoX / LFS). Usamos la misma que
`01-base-neonatox.sh` del proyecto neonatox-bootstrap.

```
export LFS=/mnt

# Directorios de toolchain (temporales)
mkdir -pv $LFS/{tools,cross,sources,build}

# usr merge
mkdir -pv $LFS/usr/{bin,lib,lib32}
ln -sfv usr/bin   $LFS/bin
ln -sfv usr/bin   $LFS/sbin
ln -sfv usr/lib   $LFS/lib
ln -sfv usr/lib   $LFS/lib64
ln -sfv usr/lib32 $LFS/lib32
ln -sfv bin       $LFS/usr/sbin

# Sistemas de archivos virtuales
mkdir -pv $LFS/{dev,proc,sys,run}

# Directorios del sistema
mkdir -pv $LFS/{boot,home,mnt,opt,srv}

# Configuración
mkdir -pv $LFS/etc/{opt,sysconfig,skel,profile.d}
mkdir -pv $LFS/etc/polkit-1/rules.d

# Librerías de kernel
mkdir -pv $LFS/lib/firmware

# Media
mkdir -pv $LFS/media/{floppy,cdrom}

# usr/share completo
mkdir -pv $LFS/usr/{,local/}{include,src}
mkdir -pv $LFS/usr/lib/locale
mkdir -pv $LFS/usr/local/{bin,lib,sbin}
mkdir -pv $LFS/usr/{,local/}share/{color,dict,doc,info,locale,man}
mkdir -pv $LFS/usr/{,local/}share/{misc,terminfo,zoneinfo}
mkdir -pv $LFS/usr/{,local/}share/man/man{1..8}

# var
mkdir -pv $LFS/var/{cache,local,log,mail,opt,spool}
mkdir -pv $LFS/var/lib/{color,misc,locate}
ln -sfv /run     $LFS/var/run
ln -sfv /run/lock $LFS/var/lock

# root y tmp
install -dv -m 0750 $LFS/root
install -dv -m 1777 $LFS/tmp $LFS/var/tmp
```

**Nota**: para la toolchain stage 1 (capítulos 2-5) solo se necesita la
estructura mínima. Pero tener todo desde el principio no molesta y deja el
sistema listo cuando completemos la instalación.

**Importante — musl y el directorio lib64**: musl no busca bibliotecas en
`/usr/lib64`. A diferencia de glibc, el loader de musl solo examina
`/lib`, `/usr/lib` y las rutas listadas en `/etc/ld-musl-x86_64.path`.
GCC por defecto instala bibliotecas de 64 bits en `/usr/lib64`, lo que
rompe todos los binarios C++ dinámicos dentro del chroot. Por eso:
- NO se crea `/usr/lib64` en la estructura (solo `/lib64 -> usr/lib`
  para mantener compatibilidad con binarios que usan ruta absoluta `/lib64`).
- **Toda compilación de GCC** debe incluir `--libdir=/usr/lib`.
- Si ya compilaste GCC sin esa opción, migra las bibliotecas de
  `/usr/lib64` a `/usr/lib` con:
  ```
  cd /usr/lib64
  for f in *; do [ ! -e "/usr/lib/$f" ] && mv "$f" "/usr/lib/$f"; done
  cd /usr && rmdir lib64 && ln -sf lib lib64
  ```
  O crea `/etc/ld-musl-x86_64.path` con el contenido `/usr/lib64`.
  > **Cuidado**: ese archivo **reemplaza** las rutas por defecto de musl,
  > no las extiende. Si solo pones `/usr/lib64`, musl deja de buscar en
  > `/lib` y `/usr/lib`, rompiendo todo el sistema.
```

---

## 1.6 — Copiar musl-cross

```
cp -a /opt/cross-x86_64-linux-musl/* $LFS/cross/
```

Verificar:

```
$LFS/cross/bin/x86_64-linux-musl-gcc --version
$LFS/cross/bin/x86_64-linux-musl-gcc -dumpmachine
# → x86_64-linux-musl
```

Este compilador depende del glibc del host para funcionar. Eso es normal.
Lo usamos solo para la etapa inicial (compilar musl dinámico y GCC nativo).

---

## 1.7 — Variables de entorno

```
export LFS=/mnt
export PATH="$LFS/cross/bin:$PATH"
export MAKEFLAGS="-j$(nproc)"
export LC_ALL=POSIX
```

Agregar opcionalmente a `~/.bashrc` para no tener que escribirlas cada vez.

---

## 1.8 — Descargar fuentes (tarballs)

```
cd $LFS/sources

# musl 1.2.6
wget https://musl.libc.org/releases/musl-1.2.6.tar.gz

# binutils 2.46.1
wget https://sourceware.org/pub/binutils/releases/binutils-2.46.1.tar.xz

# GCC 16.1.0
wget https://ftpmirror.gnu.org/gcc/gcc-16.1.0/gcc-16.1.0.tar.xz

# Linux API headers
wget https://cdn.kernel.org/pub/linux/kernel/v7.x/linux-7.1.tar.xz

# GMP 6.3.0 (para GCC)
wget https://gmplib.org/download/gmp/gmp-6.3.0.tar.xz

# MPFR 4.2.1 (para GCC)
wget https://ftp.gnu.org/gnu/mpfr/mpfr-4.2.1.tar.xz

# MPC 1.3.1 (para GCC)
wget https://ftp.gnu.org/gnu/mpc/mpc-1.3.1.tar.gz
```

Verificar integridad (sha256):

```
sha256sum musl-1.2.5.tar.gz
```

---

## 1.9 — Estructura final

```
/mnt/
├── cross/             # musl-cross semilla (glibc-host dependiente)
│   ├── bin/
│   ├── lib/
│   ├── libexec/
│   └── ...
├── tools/             # Toolchain temporal (stage 1) — vacío por ahora
├── sources/           # Tarballs descargados
├── build/             # Directorios de compilación
└── usr/               # Sistema final (stage 2)
```

---

## 1.10 — Verificación final

```
echo $LFS       # → /mnt
echo $PATH      # → .../cross/bin:...
x86_64-linux-musl-gcc --version
ls $LFS/sources/*.tar.*   # tarballs listos
```

---

## Receta nhoid documentada: musl-cross

```nhoid
#%NHO-0.5
# Package Maintainer:	cargabsj175 <cargabsj175@gmail.com>

# Name:	musl-cross-make
# Version:	0.9.11
# Release:	n2026
# License:	custom
# Repository:	extra
# Arch:	x86_64
# Url:	https://github.com/richfelker/musl-cross-make
# Description:	Cross-compiler GCC + musl estático (semilla para toolchain)
# Packageurl:	https://github.com/richfelker/musl-cross-make.git
# Packageref:	v0.9.11

nbuild() {
cat > config.mak << EOF
TARGET = x86_64-linux-musl
OUTPUT = /opt/cross-x86_64-linux-musl
EOF
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

**Nota**: musl-cross se compila en el host (con glibc). El binario resultante
se copia a `$LFS/cross/`. No es un paquete que se instale con nhopkg dentro
del sistema musl, sino una herramienta externa de bootstrap.

---

## Siguiente paso

[02-musl-dinamico.md](02-musl-dinamico.md) — Compilar musl 1.2.5 como shared
library en `$LFS/tools`.
