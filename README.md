# Libro Base: NeonatoX Toolchain musl

Construcción de una toolchain musl desde semilla, basada en la metodología LFS.

## Secuencia de capítulos

```
Fase HOST — Stage 1 (compilas desde tu PC, con glibc, en /mnt/tools)
├── 01-entorno.md          ← Prepara /mnt, copia musl-cross
├── 02-binutils.md         ← Linux headers + binutils 2.46.1
├── 03-gcc-minimal.md      ← GCC pass 1 (C estático)
├── 04-musl-dinamico.md    ← musl 1.2.6 shared library
├── 05-gcc-completo.md     ← GCC 16.1.0 completo (C++, shared, threads)
│                            Fin de stage 1: toolchain glibc-linked
│
Fase CROSS — Stage 2 (desde host glibc, compilando para musl en /mnt/usr)
├── 06-linux-headers.md    ← Linux API headers → /mnt/usr
├── 07-binutils-stage2.md  ← Binutils musl nativos → /mnt/usr
├── 08-musl-stage2.md      ← musl shared + static → /mnt/usr
├── 09-gcc-stage2.md       ← GCC musl nativo completo → /mnt/usr
├── 10-busybox.md          ← Busybox estático musl
├── 11-bash.md             ← Bash estático musl
│
Fase CHROOT (dentro del chroot musl, sin nada del host)
├── 12-archivos-sistema.md ← /etc/passwd, group, shadow, resolv.conf
├── 13-make-pass1.md       ← Make 4.4.1 (bootstrap, host→musl)
├── 14-chroot-musl.md      ← Chroot limpio con sistema musl nativo
├── 15-make-pass2.md       ← Make 4.4.1 (recompilación nativa)
├── 16-ncurses.md          ← ncurses 6.6
├── 17-readline.md         ← readline 8.3
├── 18-bash-completo.md    ← Bash dinámico musl
├── 19-nano.md             ← nano 9.0
├── 20-file.md             ← file 5.48
├── 21-m4.md               ← m4 1.4.21
├── 22-musl-rebuild.md     ← musl rebuild nativo
├── 23-binutils-rebuild.md ← binutils rebuild nativo
├── 24-gmp.md              ← GMP 6.3.0 (para GCC)
├── 25-mpfr.md             ← MPFR 4.2.1
├── 26-mpc.md              ← MPC 1.3.1
├── 27-gcc-rebuild.md      ← GCC rebuild nativo (--enable-default-pie)
├── 28-libstdcxx.md        ← libstdc++ rebuild independiente
├── 29-zstd.md             ← zstd 1.5.7
├── 30-zlib.md             ← zlib 1.3.2
├── 31-perl.md             ← Perl 5.42.2
├── 32-openssl.md          ← OpenSSL 4.0.1
├── 33-pkgconf.md          ← pkgconf 2.5.1
├── 34-libffi.md           ← libffi 3.5.2 (Python ctypes)
├── 35-flex.md             ← flex 2.6.4
├── 36-bison.md            ← bison 3.8.2
├── 37-expat.md            ← expat 2.7.1 (XML parser)
├── 38-sqlite.md           ← SQLite 3.51.0
├── 39-python.md           ← Python 3.14.6
├── 40-ninja-meson.md      ← ninja 1.13.2 + meson 1.11.1
├── 41-linux-pam.md        ← Linux-PAM 1.7.0
├── 42-shadow.md           ← shadow 4.19.4 (useradd, login, su)
├── 43-e2fsprogs.md        ← e2fsprogs 1.47.4 (mkfs.ext*)
├── 44-util-linux.md       ← util-linux 2.44.1 (mount, blkid, fdisk)
├── 45-lzo.md              ← LZO 2.10 (btrfs-progs)
├── 46-btrfs-progs.md      ← btrfs-progs 6.14
├── 47-grep.md             ← Grep 3.12
├── 48-libpsl.md           ← libpsl 0.21.5 (curl dep)
├── 49-nghttp2.md          ← nghttp2 1.66.0 (curl dep)
├── 50-curl.md             ← Curl 8.20.0
├── 51-findutils.md        ← Findutils 4.10.0
├── 52-autoconf.md         ← autoconf 2.72
├── 53-automake.md         ← automake 1.18.1
├── 54-pcre2.md            ← pcre2 10.47
├── 55-flit-core.md        ← flit-core 3.12.0 (Python)
├── 56-packaging.md        ← packaging 26.2 (Python)
├── 57-wheel.md            ← wheel 0.46.1 (Python)
├── 58-setuptools.md       ← setuptools 82.0.1 (Python)
├── 59-markupsafe.md       ← Markupsafe 3.0.3 (Python)
├── 60-jinja2.md           ← Jinja2 3.1.6 (Python)
├── 61-gperf.md            ← gperf 3.3
├── 62-gettext.md          ← gettext (i18n)
├── 63-coreutils.md        ← Coreutils 9.11
│
Anexos
├── 99-git.md              ← Git 2.49.0
├── 99-plocate.md          ← plocate 1.1.23
└── 99-systemd.md          ← systemd 260.2
```

## Convenciones

- `$LFS=/mnt` — punto de montaje del sistema destino
- host = tu PC normal (con glibc)
- chroot = entorno musl (después del capítulo 12)
- Cada capítulo incluye la receta `nhoid` al final
