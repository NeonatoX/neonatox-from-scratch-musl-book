# Capítulo 60: Systemd 260.2

## 60.1 — Objetivo

The systemd package contains programs for controlling the startup, running, and shutdown of the system. 
   
## 60.2 — Descargar

```
wget https://github.com/systemd/systemd/archive/v260.2/systemd-260.2.tar.gz

```

## 60.3 — Compilar

```
cd /sources
tar xvf systemd-260.2.tar.gz
cd systemd-260.2

sed -i 's/SYSTEMD_TEST_DATA=%q\\nSYSTEMD_CATALOG_DIR=%q\\n/SYSTEMD_TEST_DATA=%s\\nSYSTEMD_CATALOG_DIR=%s\\n/g' meson.build

mkdir -p build && cd build

meson setup ..                \
      --prefix=/usr           \
      --buildtype=release     \
      -D libc=musl            \
      -D nss-systemd=false    \
      -D nss-myhostname=false \
      -D nss-resolve=disabled    \
      -D nss-mymachines=disabled \
      -D userdb=false         \
      -D default-dnssec=no    \
      -D firstboot=false      \
      -D install-tests=false  \
      -D ldconfig=false       \
      -D sysusers=false       \
      -D rpmmacrosdir=no      \
      -D homed=disabled       \
      -D man=disabled         \
      -D mode=release         \
      -D pam=enabled          \
      -D pamconfdir=/etc/pam.d \
      -D dev-kvm-mode=0660    \
      -D nobody-group=nogroup \
      -D sysupdate=disabled   \
      -D ukify=disabled       \
      -D tests=false          \
      -D docdir=/usr/share/doc/systemd
ninja

nija install

```

## 60.4 — Verificar

```
automake --version
# automake (GNU Automake) 1.18.1

```
