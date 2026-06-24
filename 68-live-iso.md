# Capítulo 68: Live ISO booteable

## 68.1 — Objetivo

Generar una ISO híbrida (BIOS+UEFI) del sistema musl nativo usando
`neonatox-live-boot`. La ISO arranca con systemd, initramfs con
busybox, squashfs del sistema y overlay tmpfs para escritura.

## 68.2 — Prerrequisitos

- Sistema musl funcional (capítulos 01-67)
- `neonatox-live-boot` clonado en `/sources/`
- Herramientas del initramfs compiladas (capítulo 65)
- Kernel con soporte: `squashfs`, `loop`, `overlay`, `zstd`
- `xorriso` instalado (libisoburn)

## 68.3 — Estructura del repositorio

```
neonatox-live-boot/
├── build.sh           ← Script principal: genera la ISO completa
├── build-tools.sh     ← Compila binarios estáticos para initramfs
├── initramfs/         ← Contenido del initramfs
│   ├── init           ← PID 1 (busybox ash)
│   └── ...            ← busybox, bash, e2fsprogs, etc.
├── EXCLUDES           ← Patrones a excluir del squashfs
└── tools/             ← Fuentes descargadas por build-tools.sh
```

## 68.4 — Compilar herramientas del initramfs

Ver capítulo 65.

```bash
cd /sources/neonatox-live-boot
./build-tools.sh --busybox
./build-tools.sh --bash        # opcional
./build-tools.sh --e2fsprogs   # opcional, para mkfs.ext4
```

## 68.5 — Generar la ISO

Ejecutar como **root**:

```bash
cd /sources/neonatox-live-boot
./build.sh
```

### Qué hace build.sh

1. Lee `EXCLUDES` y genera lista de exclusión
2. Crea sistema de archivos temporal en `/tmp/live-iso/`
3. Ejecuta `mksquashfs / directorio-temporal/live/filesystem.squashfs` con `-comp zstd`
4. Copia kernel desde `BOOT_IMAGE` (detectado de `/proc/cmdline`)
5. Crea initramfs desde `initramfs/`:
   - Popula `/bin`, `/sbin`, `/lib`, `/etc`, etc.
   - Copia busybox + bash + e2fsprogs
   - Copia kernel modules del host
   - Empaqueta con `find . | cpio -o -H newc | gzip`
6. Configura GRUB para BIOS legacy (`boot/grub/grub.cfg`)
7. Configura GRUB para UEFI (`EFI/BOOT/`)
8. Ejecuta `grub-mkrescue` o `xorriso` para generar ISO híbrida

### Initramfs Init (initramfs/init)

El script `initramfs/init` es PID 1 y realiza:

1. Monta `proc`, `sysfs`, `devtmpfs`
2. Carga módulos del kernel (USB, SATA, NVMe, HID, filesystem)
3. Escanea dispositivos de bloque (`/dev/sr*`, `/dev/sd*`, `/dev/nvme*`,
   `/dev/mmcblk*`, `/dev/vd*`, `/dev/xvd*`) buscando `rootfs.squashfs`
4. Monta squashfs como `lowerdir`
5. Crea overlay tmpfs (o zram si hay suficiente RAM) como `upperdir`
6. Transiciona con `exec switch_root` a systemd

## 68.6 — Personalización

### EXCLUDES

El archivo `EXCLUDES` lista paths que NO deben ir en el squashfs:

```
/proc/*
/sys/*
/dev/*
/run/*
/tmp/*
/var/cache/*
/var/log/journal/*
/mnt/*
/media/*
/lost+found
/root/.bash_history
/home/*/.bash_history
```

Editar antes de ejecutar `build.sh` si es necesario.

### Añadir paquetes

Para incluir paquetes extra en la ISO, instalarlos en el sistema
antes de ejecutar `build.sh`.

## 68.7 — Verificar

```bash
ls -lh /tmp/neonatox-musl-*.iso

file /tmp/neonatox-musl-*.iso
# → ISO 9660 CD-ROM filesystem data ... 'NEONATOX_MUSL'
# → DOS/MBR boot sector (isohybrid)

# Probar en QEMU
qemu-system-x86_64 -cdrom /tmp/neonatox-musl-*.iso -m 2048
```

## 68.8 — Notas

- La ISO es **híbrida**: bootea en BIOS legacy y UEFI
- El overlay es **tmpfs** (volátil): los cambios se pierden al
  reiniciar. Para persistencia, añadir partición `live-rw`
- El tamaño de la ISO depende del sistema instalado (fácilmente 2-4GB)
- `build.sh` debe ejecutarse como root para acceder a `/` y `/proc`
- Los módulos del kernel se copian automáticamente del host
