# Capítulo 10: Busybox Maximal (Estático musl)

## 10.1 — Objetivo

Compilar busybox estático con todos los applets habilitados, linkado
contra musl. Sirve como sistema base mínimo (shell, coreutils, init,
network tools) dentro del chroot y eventualmente en el sistema final.

## 10.2 — Dependencias

- `x86_64-linux-musl-gcc` (cross-compiler o GCC nativo musl)
- Linux API headers en `$LFS/usr/include`

---

## 10.3 — Compilar

```bash
cd $LFS/sources
# Descargar si no existe: https://busybox.net/downloads/busybox-1.36.1.tar.bz2
# O usar busybox git

tar -xf busybox-1.36.1.tar.bz2
cd busybox-1.36.1

# Configuración maximal
make allyesconfig

# Forzar estático y musl
sed -i 's/.*CONFIG_STATIC.*/CONFIG_STATIC=y/' .config
sed -i 's/.*CONFIG_STATIC_LIBGCC.*/CONFIG_STATIC_LIBGCC=y/' .config

# Cross-compilar
make -j$(nproc) CC=x86_64-linux-musl-gcc 2>&1 | tee build.log

make CC=x86_64-linux-musl-gcc CONFIG_PREFIX=$LFS/usr install

# Alternativa: instalar en /bin
cp busybox $LFS/bin/busybox

# Instalar applets
for applet in $(./busybox --list); do
  ln -sf busybox $LFS/usr/bin/$applet
done
```

### Nota

Si ya tenés busybox compilado en `$LFS/bin/busybox`, podés skipear
la compilación y solo crear los symlinks.

---

## 10.4 — Verificar

```bash
file $LFS/bin/busybox
# → ELF ... statically linked, musl

$LFS/bin/busybox --list | head -5
# → ash, cat, chmod, cp, ...
```

---

<< [Anterior](09-gcc-stage2.md) | [Índice](README.md) | [Siguiente](11-make.md) >>
