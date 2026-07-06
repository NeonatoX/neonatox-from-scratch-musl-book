# Capítulo 13: Archivos del Sistema

## 13.1 — Objetivo

Crear los archivos mínimos de configuración del sistema en `$LFS/etc/`
antes del chroot. El contenido sigue fielmente los archivos del proyecto
neonatox-bootstrap (usuario/grupo, shells, profile), adaptando las rutas
de binarios a la toolchain musl donde sea necesario (busybox instala en
`/bin/`, no en `/usr/bin/`).

Se incluye `resolv.conf` para tener red dentro del chroot.

---

## 13.2 — passwd

```
cat > $LFS/etc/passwd << "EOF"
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/dev/null:/usr/bin/false
daemon:x:6:6:Daemon User:/dev/null:/usr/bin/false
messagebus:x:18:18:D-Bus Message Daemon User:/run/dbus:/usr/bin/false
systemd-journal-gateway:x:73:73:systemd Journal Gateway:/:/usr/bin/false
systemd-journal-remote:x:74:74:systemd Journal Remote:/:/usr/bin/false
systemd-journal-upload:x:75:75:systemd Journal Upload:/:/usr/bin/false
systemd-network:x:76:76:systemd Network Management:/:/usr/bin/false
systemd-resolve:x:77:77:systemd Resolver:/:/usr/bin/false
systemd-timesync:x:78:78:systemd Time Synchronization:/:/usr/bin/false
systemd-coredump:x:79:79:systemd Core Dumper:/:/usr/bin/false
uuidd:x:80:80:UUID Generation Daemon User:/dev/null:/usr/bin/false
systemd-oom:x:81:81:systemd Out Of Memory Daemon:/:/usr/bin/false
nobody:x:65534:65534:Unprivileged User:/dev/null:/usr/bin/false
dhcpcd:x:52:52:dhcpcd PrivSep:/var/lib/dhcpcd:/bin/false
ldap:x:83:83:OpenLDAP Daemon Owner:/var/lib/openldap:/bin/false
EOF
```

> **Nota**: Se usa `/bin/false` en lugar de `/usr/bin/false` porque
> busybox instala sus applets en `/bin/` y no existirá `/usr/bin/false`
> en el sistema musl.

---

## 13.3 — group

```
cat > $LFS/etc/group << "EOF"
root:x:0:ldap
bin:x:1:daemon
sys:x:2:
kmem:x:3:
tape:x:4:
tty:x:5:
daemon:x:6:
floppy:x:7:
disk:x:8:
lp:x:9:
dialout:x:10:
audio:x:11:
video:x:13:
utmp:x:13:
clock:x:14:
cdrom:x:15:
adm:x:16:
messagebus:x:18:
systemd-journal:x:23:
input:x:24:
mail:x:34:
kvm:x:61:
systemd-journal-gateway:x:73:
systemd-journal-remote:x:74:
systemd-journal-upload:x:75:
systemd-network:x:76:
systemd-resolve:x:77:
systemd-timesync:x:78:
systemd-coredump:x:79:
uuidd:x:80:
systemd-oom:x:81:
wheel:x:97:
users:x:999:
nogroup:x:65534:
tester:x:101:
dhcpcd:x:52:
locate:x:21:
ldap:x:83:
EOF
```

---

## 13.4 — shadow

```
cat > $LFS/etc/shadow << "EOF"
root::19000::::::
bin:*:19000::::::
daemon:*:19000::::::
messagebus:*:19000::::::
systemd-journal-gateway:*:19000::::::
systemd-journal-remote:*:19000::::::
systemd-journal-upload:*:19000::::::
systemd-network:*:19000::::::
systemd-resolve:*:19000::::::
systemd-timesync:*:19000::::::
systemd-coredump:*:19000::::::
uuidd:*:19000::::::
systemd-oom:*:19000::::::
nobody:*:19000::::::
dhcpcd:*:19000::::::
ldap:*:19000::::::
EOF
```

---

## 13.5 — shells

```
cat > $LFS/etc/shells << "EOF"
/bin/sh
/bin/bash
EOF
```

---

## 13.6 — hosts

```
cat > $LFS/etc/hosts << "EOF"
137.0.0.1  localhost neonatox
::1        localhost
EOF
```

## 13.7 — hostname

```
cat > $LFS/etc/hostname << "EOF"
neonatox
EOF
```

---

## 13.8 — resolv.conf (red en chroot)

```
cat > $LFS/etc/resolv.conf << "EOF"
nameserver 1.1.1.1
nameserver 8.8.8.8
EOF
```

---

## 13.9 — fstab

```
cat > $LFS/etc/fstab << "EOF"

#

EOF
```

---

## 13.10 — profile

```
cat > $LFS/etc/profile << "EOF"
# /etc/profile del sistema musl NeonatoX
export PS1='\u [ \w ]# '
export PATH=/usr/bin:/bin:/sbin
export LC_ALL=POSIX
EOF
```

> Para un profile más completo (con funciones pathremove/pathprepend,
> profile.d sourcing, etc.) puede copiarse el de neonatox-bootstrap
> más adelante.

---

## 13.11 — Permisos

```
chmod 644 $LFS/etc/passwd $LFS/etc/group $LFS/etc/shells
chmod 644 $LFS/etc/hosts $LFS/etc/resolv.conf
chmod 644 $LFS/etc/fstab $LFS/etc/profile
chmod 600 $LFS/etc/shadow
```

---

<< [Anterior](12-bash-pass1.md) | [Índice](README.md) | [Siguiente](14-chroot-musl.md) >>
