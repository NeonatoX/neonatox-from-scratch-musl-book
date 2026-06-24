# Capítulo 46: btrfs-progs 6.14

## 46.1 — Objetivo

Compilar btrfs-progs en `/usr`. Proporciona herramientas de gestión
para el sistema de archivos btrfs, que es el filesystem primario del
proyecto (usado desde Fase 0 con `compress=zstd:3`).

## 46.2 — Dependencias

- gcc, make (ya en el sistema)
- util-linux 2.42.1 (capítulo 42) — libblkid, libuuid
- zstd 1.5.7 (capítulo 29)
- LZO 2.10 (capítulo 44, opcional para compresión lzo en btrfs)
- Python 3.14.6 (capítulo 38, para bindings Python)
- systemd (opcional, para unit files)

## 46.3 — Descargar

```
wget https://mirrors.kernel.org/pub/linux/kernel/people/kdave/btrfs-progs/btrfs-progs-v6.14.tar.xz
```

## 46.4 — Compilar

```
cd /sources
rm -rf btrfs-progs-v6.14
tar xf btrfs-progs-v6.14.tar.xz
cd btrfs-progs-v6.14

./configure --prefix=/usr           \
            --disable-libudev       \
            --disable-backtrace     \
            --disable-documentation \
            --enable-convert        \
            --disable-python        \
            --disable-libbtrfsutil-python

make -j$(nproc)
make install
```

## 46.5 — Verificar

```
btrfs --version
# → btrfs-progs v6.14

mkfs.btrfs -V
# → mkfs.btrfs part of btrfs-progs v6.14

file /usr/bin/btrfs
# → ELF ... dynamically linked ... interpreter /lib/ld-musl-x86_64.so.1
```

## 46.6 — Notas

- `--disable-libudev`: no se necesita detección de dispositivos udev
  durante stage 2 (systemd no está presente).
- `--disable-backtrace`: evita dependencia de libexecinfo (no
  disponible en musl).
- `--disable-documentation`: evita dependencia de asciidoc/xmlto.
- `--enable-convert`: activa `btrfs-convert` (ext2/3/4 → btrfs).
  Requiere e2fsprogs 1.47.4 (capítulo 43) — ya instalado.
- `--enable-python`: activa bindings Python para `librbtrfs` y
  `btrfs`. Requiere Python 3.14.6 (capítulo 38) — ya instalado.
- Las herramientas instaladas incluyen: `btrfs`, `mkfs.btrfs`,
  `btrfstune`, `btrfsck`, `btrfs-image`, `btrfs-map-logical`,
  `btrfs-select-super`.
- Btrfs funciona sin problemas en musl — usa syscales del kernel
  directamente.
- El sistema usa btrfs con `compress=zstd:3` desde Fase 0; estas
  herramientas son necesarias para administrar subvolúmenes,
  snapshots, balanceo y scrub.
