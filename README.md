# NeonatoX Toolchain Bootstrap — musl

Libro de construcción paso a paso de una toolchain completa desde fuente
para NeonatoX con musl como libc primaria.

## Índice

| # | Capítulo | Descripción |
|---|----------|-------------|
| 01 | [Entorno](01-entorno.md) | Preparación del entorno, partición, variables |
| 02 | [Linux Headers + Binutils](02-binutils.md) | Kernel headers y binutils stage 1 |
| 03 | [GCC Mínimo](03-gcc-minimal.md) | GCC pass 1 (solo C, estático) |
| 04 | [musl Dinámico](04-musl-dinamico.md) | musl 1.2.6 shared library |
| 05 | [GCC Completo](05-gcc-completo.md) | GCC pass 1 completo (C++, shared, threads) |
| 06 | [Linux Headers Stage 2](06-linux-headers.md) | Kernel headers para `$LFS/usr` |
| 07 | [Binutils Stage 2](07-binutils-stage2.md) | Binutils nativos musl |
| 08 | [musl Stage 2](08-musl-stage2.md) | musl 1.2.6 en `$LFS/usr` |
| 09 | [GCC Stage 2](09-gcc-stage2.md) | GCC de transición (cross glibc→musl) |
| 10 | [Busybox](10-busybox.md) | Busybox maximal estático |
| 11 | [Make](11-make.md) | Make dinámico musl |
| 12 | [Bash Pass 1](12-bash-pass1.md) | Bash estático musl |
| 13 | [Archivos del Sistema](13-archivos-sistema.md) | /etc/passwd, group, shadow, profile |
| 14 | [Chroot musl](14-chroot-musl.md) | Primer chroot con sistema musl nativo |
| 15 | [ncurses](15-ncurses.md) | ncurses 6.6 (widec) |
| 16 | [readline](16-readline.md) | readline 8.3 |
| 17 | [Bash Final](17-bash-final.md) | Bash dinámico con readline |
| 18 | [nano](18-nano.md) | nano 9.0 |
| 19 | [file](19-file.md) | file 5.48 |
| 20 | [m4](20-m4.md) | m4 1.4.21 |
| 21 | [GMP](21-gmp.md) | GMP 6.3.0 |
| 22 | [MPFR](22-mpfr.md) | MPFR 4.2.1 |
| 23 | [MPC](23-mpc.md) | MPC 1.3.1 |
| 24 | [zlib](24-zlib.md) | zlib 1.3.2 |
| 25 | [Gawk](25-gawk.md) | Gawk 5.4.0 |
| 26 | [Sed](26-sed.md) | Sed 4.10 |
| 27 | [GCC Final](27-gcc-final.md) | GCC completamente nativo |
| 28 | [zstd](28-zstd.md) | zstd 1.5.7 compresión |
| 29 | [Perl](29-perl.md) | Perl 5.40.0 |
| 30 | [OpenSSL](30-openssl.md) | OpenSSL 3.5.0 |
| 31 | [pkgconf](31-pkgconf.md) | pkgconf 2.4.0 |
| 32 | [libffi](32-libffi.md) | libffi 3.5.2 |
| 33 | [Flex](33-flex.md) | Flex 2.6.4 |
| 34 | [Bison](34-bison.md) | Bison 3.8.2 |
| 35 | [Expat](35-expat.md) | Expat 2.7.1 |
| 36 | [SQLite](36-sqlite.md) | SQLite 3.49.1 |
| 37 | [Python](37-python.md) | Python 3.13.0 |
| 38 | [Ninja + Meson](38-ninja-meson.md) | Ninja 1.12.1 + Meson 1.7.0 |
| 39 | [libarchive](39-libarchive.md) | libarchive 3.8.4 |
| 40 | [Findutils](40-findutils.md) | Findutils 4.10.0 |
| 41 | [plocate](41-plocate.md) | plocate 1.1.24 |
| 42 | [Gettext](42-gettext.md) | Gettext 1.0 |
| 43 | [Grep](43-grep.md) | Grep 3.12 |
| 44 | [libpsl](44-libpsl.md) | libpsl 0.21.5 |
| 45 | [nghttp2](45-nghttp2.md) | nghttp2 1.65.0 |
| 46 | [cURL](46-curl.md) | cURL 8.20.0 |
| 47 | [Autoconf](47-autoconf.md) | Autoconf 2.72 |
| 48 | [Automake](48-automake.md) | Automake 1.18.1 |
| 49 | [PCRE2](49-pcre2.md) | PCRE2 10.45 |
| 50 | [Git](50-git.md) | Git 2.54.0 |
| 51 | [nhopkg](51-nhopkg.md) | nhopkg package manager |
| 52 | [util-linux](52-util-linux.md) | util-linux 2.41 |
| 53 | [gperf](53-gperf.md) | gperf 3.3 |
| 54 | [Coreutils](54-coreutils.md) | Coreutils 9.11 |
| 55 | [LZO](55-lzo.md) | LZO 2.10 |
| 56 | [Cpio](56-cpio.md) | Cpio 2.15 |
| 57 | [CrackLib](57-cracklib.md) | CrackLib 2.10.3 |
| 58 | [Linux PAM](58-linux-pam.md) | Linux PAM 1.7.0 |
| 59 | [flit-core](59-flit-core.md) | flit-core 3.12.0 |
| 60 | [Packaging](60-packaging.md) | Python packaging 24.2 |
| 61 | [Wheel](61-wheel.md) | Python wheel 0.46.1 |
| 62 | [Setuptools](62-setuptools.md) | Setuptools 76.1.0 |
| 63 | [libpwquality](63-libpwquality.md) | libpwquality 1.4.5 |
| 64 | [Attr](64-attr.md) | Attr 2.5.2 |
| 65 | [ACL](65-acl.md) | ACL 2.3.2 |
| 66 | [libcap](66-libcap.md) | libcap 2.75 |
| 67 | [Shadow](67-shadow.md) | Shadow 4.17.3 |
| 68 | [MarkupSafe](68-markupsafe.md) | MarkupSafe 3.0.2 |
| 69 | [Jinja2](69-jinja2.md) | Jinja2 3.1.6 |
| 70 | [systemd](70-systemd.md) | systemd 260.2 |
| 71 | [texinfo](71-texinfo.md) | texinfo 7.3 |
| 71 | [btrfs-progs](72-btrfs-progs.md) | btrfs-progs 6.14 |
| 72 | [e2fsprogs](73-e2fsprogs.md) | e2fsprogs 1.47.4 |
| 73 | [Initramfs Tools](73-neonatox-initramfs-tools.md) | neonatox-mkinitramfs |
| — | [Live ISO](99-live-iso.md) | Construcción de ISO en vivo |
| — | [Primer Arranque](99-primer-arranque.md) | Primer arranque del sistema musl |

## Leyenda

- **Stage 1**: Toolchain temporal en `$LFS/tools`, compilada con musl-cross
- **Stage 2**: Toolchain final en `$LFS/usr`, compilada desde glibc host
- **Chroot**: Construcción dentro del sistema musl nativo

## Referencias

- [AGENTS.md](AGENTS.md) — Guía para asistentes IA
- [Proyecto original neonatox-bootstrap](https://gitlab.com/neonatox/neonatox-bootstrap)
