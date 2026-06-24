# Capítulo 41: Linux-PAM 1.7.0

## 41.1 — Objetivo

Compilar Linux-PAM 1.7.0 en `/usr`. Pluggable Authentication Modules —
sistema de autenticación modular. Necesario para shadow (login, su,
passwd con PAM).

## 41.2 — Dependencias

- gcc, make, ninja, meson (ya en el sistema)
- flex 2.6.4 (capítulo 35)
- bison 3.8.2 (capítulo 36)

## 41.3 — Descargar

```
wget https://github.com/linux-pam/linux-pam/releases/download/v1.7.0/Linux-PAM-1.7.0.tar.xz
```

## 41.4 — Compilar

```bash
cd /sources
tar xf Linux-PAM-1.7.0.tar.xz
cd Linux-PAM-1.7.0

meson setup build \
      --prefix=/usr \
      --libdir=/usr/lib \
      -Ddisable-nls=true \
      -Dregenerate-docu=false

ninja -C build
ninja -C build install

# Configuración básica
mkdir -pv /etc/pam.d

cat > /etc/pam.d/other << "EOF"
auth     required       pam_deny.so
account  required       pam_deny.so
password required       pam_deny.so
session  required       pam_deny.so
EOF
```

## 41.5 — Verificar

```bash
ldd /usr/lib/libpam.so
# → libc.so => /lib/ld-musl-x86_64.so.1
```

## 41.6 — Notas

- PAM 1.7.0 usa meson como build system (no autotools)
- `-Ddisable-nls=true`: desactiva traducciones
- `-Dregenerate-docu=false`: evita dependencias de docbook
- Se crea `/etc/pam.d/other` con política denegada por defecto
- En musl, PAM compila sin problemas
- `libpam.so`, `libpam_misc.so`, `libpamc.so` se instalan en `/usr/lib/`
