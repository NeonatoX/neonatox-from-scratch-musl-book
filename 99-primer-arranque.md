# Capítulo 67: Primer Arranque

## 67.1 — Objetivo

Configurar el sistema musl nativo en `$LFS` para que arranque por sí
solo: bootloader, kernel, initramfs, y archivos mínimos de sistema.

## 67.2 — Prerrequisitos

- Fase 3 completa (toolchain + sistema en `$LFS/usr/`)
- systemd instalado (capítulo 64)
- busybox con applets instalados (capítulo 10)
- Kernel compilado o preexistente en `$LFS/boot/`

## 67.3 — Archivos de sistema

Crear los archivos mínimos que systemd espera:

```bash
# Identificación del sistema
cat > /etc/os-release << "EOF"
NAME="NeonatoX"
VERSION="musl-2026.06"
ID=neonatox
PRETTY_NAME="NeonatoX musl (2026.06)"
ANSI_COLOR="0;36"
EOF

# Nombre de host
echo "neonatox-musl" > /etc/hostname

# Locale
cat > /etc/locale.conf << "EOF"
LANG=C.UTF-8
LC_ALL=C.UTF-8
EOF

# máquina-id (systemd la genera automáticamente en tmpfs si no existe,
# pero crearla evita warnings)
systemd-machine-id-setup
```

## 67.4 — Kernel

Hay dos opciones:

### Opción A: Usar kernel preexistente del host

Si ya hay un kernel en `$LFS/boot/` compatible con musl:

```bash
ls -la /boot/vmlinuz-*
# Debe mostrar un kernel existente
```

### Opción B: Compilar kernel contra musl

```bash
cd /sources
rm -rf linux-7.1
tar -xf linux-7.1.tar.xz
cd linux-7.1

make mrproper
make defconfig

# Configuraciones recomendadas para musl:
# - CONFIG_UEVENT_HELPER=y (para busybox mdev)
# - CONFIG_DEVTMPFS=y
# - CONFIG_TMPFS=y
# - CONFIG_SQUASHFS=y (para live)
# - CONFIG_BTRFS_FS=y (para FS destino)
scripts/config --enable CONFIG_UEVENT_HELPER
scripts/config --enable CONFIG_DEVTMPFS
scripts/config --enable CONFIG_TMPFS
scripts/config --enable CONFIG_BTRFS_FS
scripts/config --enable CONFIG_SQUASHFS

# Opcional: reducir tamaño quitando debug
scripts/config --disable CONFIG_DEBUG_KERNEL

make -j$(nproc) bzImage
make modules
make modules_install
cp arch/x86_64/boot/bzImage /boot/vmlinuz-7.1.0-neonatox-musl
cp System.map /boot/System.map-7.1.0-neonatox-musl
cp .config /boot/config-7.1.0-neonatox-musl
```

## 67.5 — Initramfs

El initramfs es necesario si el root está en:
- btrfs (necesita `btrfs-progs` en initramfs)
- cifrado LUKS
- LVM
- NFS

Para sistemas simples (ext4 directo), el kernel puede bootear sin initramfs.

### Opción A: Sin initramfs (para ext4 simple)

Configurar el kernel para que monte el root directamente:

```bash
# En la config del kernel:
# CONFIG_EXT4_FS=y (no module)
```

Pasar al kernel en GRUB:
```bash
root=/dev/sdaX ro
```

### Opción B: Con neonatox-mkinitramfs

Si `neonatox-mkinitramfs` está instalado:

```bash
neonatox-mkinitramfs /boot/initramfs-7.1.0-neonatox-musl.img
```

### Opción C: Initramfs manual con busybox

```bash
mkdir -p /tmp/initramfs && cd /tmp/initramfs

# Estructura base
mkdir -p {bin,dev,etc,lib,mnt,proc,root,sbin,sys,usr/bin,usr/sbin}

# busybox estático
cp /bin/busybox bin/
for app in sh mount umount switch_root modprobe insmod lsmod mkdir; do
  ln -s /bin/busybox bin/$app
done

# Script init
cat > init << "INIT"
#!/bin/busybox sh
/bin/busybox --install -s

mount -t proc none /proc
mount -t sysfs none /sys
mount -t devtmpfs none /dev

# Cargar módulos del disco root
modprobe btrfs 2>/dev/null || true

# Montar root y transicionar
mount -t btrfs /dev/sda2 /mnt || mount -t ext4 /dev/sda3 /mnt
exec switch_root /mnt /sbin/init
INIT
chmod +x init

# Empaquetar
find . | cpio -o -H newc | gzip > /boot/initramfs-7.1.0-neonatox-musl.img
rm -rf /tmp/initramfs
```

## 67.6 — GRUB (bootloader)

### Instalar GRUB en el disco de arranque

Identificar el disco (NO la partición):

```bash
lsblk
# Ejemplo: sda es el disco, sda3 es la partición raíz
```

Instalar GRUB:

```bash
grub-install /dev/sda
```

### Configurar GRUB

```bash
cat > /boot/grub/grub.cfg << "GRUB"
set default=0
set timeout=5

menuentry "NeonatoX musl" {
  linux /boot/vmlinuz-7.1.0-neonatox-musl root=/dev/sda3 ro quiet
  initrd /boot/initramfs-7.1.0-neonatox-musl.img
}
GRUB
```

> **Nota**: Ajustar `root=` al dispositivo real donde está `$LFS`.
> Si se usó initramfs, incluir la línea `initrd`.

Para generar automáticamente:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

## 67.7 — Contraseña de root

```bash
passwd root
# Establecer una contraseña para el primer login
```

## 67.8 — Checklist pre-arranque

```bash
echo "== Checklist pre-arranque =="

echo -n "Kernel:      "; ls /boot/vmlinuz-* 2>/dev/null || echo "FALTA"
echo -n "GRUB:        "; ls /boot/grub/grub.cfg 2>/dev/null || echo "FALTA"
echo -n "initramfs:   "; ls /boot/initramfs-* 2>/dev/null || echo "OPCIONAL"
echo -n "systemd:     "; file /sbin/init 2>/dev/null | grep systemd || echo "FALTA"
echo -n "busybox:     "; file /bin/busybox 2>/dev/null | grep static || echo "FALTA"
echo -n "os-release:  "; cat /etc/os-release 2>/dev/null | grep NAME || echo "FALTA"
echo -n "hostname:    "; cat /etc/hostname 2>/dev/null || echo "FALTA"
echo -n "resolv.conf: "; grep nameserver /etc/resolv.conf 2>/dev/null || echo "FALTA"
echo -n "fstab:       "; cat /etc/fstab 2>/dev/null || echo "FALTA"
echo -n "root pass:   "; grep ^root /etc/shadow 2>/dev/null | grep -q '\$' && echo "OK" || echo "SIN CONTRASEÑA"
```

## 67.9 — Notas de solución de problemas

| Síntoma | Causa probable | Solución |
|---------|---------------|----------|
| Kernel panic — no root device | Falta driver en kernel o initramfs | Compilar driver en kernel (`=y`, no `=m`) o cargar módulo en initramfs |
| systemd no arranca | Falta `/etc/machine-id` | `systemd-machine-id-setup` |
| No hay red | Falta firmwares o driver de red | Compilar driver en kernel |
| login: no se puede ejecutar /bin/bash | bash no es estático y falta libc | `ldd /bin/bash` y verificar que `ld-musl-x86_64.so.1` está en `/lib/` |
| Input/output error en btrfs | Falta `btrfs-progs` en initramfs | Incluir `btrfs.ko` y `btrfsck` en initramfs |


---

<< [Anterior](99-live-iso.md) | [Índice](README.md) | Siguiente >>
