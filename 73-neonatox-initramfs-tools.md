# Capítulo 73: Initramfs Tools (build-tools.sh)

## 73.1 — Objetivo

Compilar las herramientas estáticas necesarias para el initramfs del
Live ISO (capítulo 68). Usa el script `build-tools.sh` de
`neonatox-live-boot`, que descarga, compila e instala binarios
completamente estáticos (musl) en `initramfs/`.

## 73.2 — Dependencias

- musl-cross toolchain host (`x86_64-linux-musl-gcc`)
- make, bison, flex
- git

## 73.3 — Descargar

```bash
cd /sources
git clone https://github.com/cargabsj175/neonatox-live-boot.git
cd neonatox-live-boot
```

## 73.4 — Compilar herramientas

```bash
./build-tools.sh --list
# Muestra las herramientas disponibles

# Compilar busybox (obligatorio)
./build-tools.sh --busybox

# Compilar bash (opcional, shell completa)
./build-tools.sh --bash

# Compilar e2fsprogs (mkfs.ext4, fsck.ext4)
./build-tools.sh --e2fsprogs
```

Cada herramienta se compila dentro de `tools/` y el binario estático
resultante se copia a `initramfs/`.

## 73.5 — Verificar

```bash
ls -la initramfs/
# Debe mostrar busybox, bash, etc.

file initramfs/busybox
# → ELF ... statically linked

file initramfs/bash
# → ELF ... statically linked

initramfs/busybox --list | grep switch_root
# → Debe incluir switch_root
```


---

<< [Anterior](72-e2fsprogs.md) | [Índice](README.md) | [Siguiente](99-live-iso.md) >>
