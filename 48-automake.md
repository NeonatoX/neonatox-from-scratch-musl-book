# Capítulo 48: Automake 1.18.1

## 48.1 — Objetivo

A GNU tool for automatically creating Makefiles.

## 48.2 — Descargar

```bash
wget https://ftp.gnu.org/gnu/automake/automake-1.18.1.tar.xz
```

## 48.3 — Compilar

```bash
cd /sources
tar xvf automake-1.18.1.tar.xz 
cd automake-1.18.1

./configure --prefix=/usr --docdir=/usr/share/doc/automake
make

make install
```

## 48.4 — Verificar

```bash
automake --version
# automake (GNU Automake) 1.18.1

```


---

<< [Anterior](47-autoconf.md) | [Índice](README.md) | [Siguiente](49-pcre2.md) >>
