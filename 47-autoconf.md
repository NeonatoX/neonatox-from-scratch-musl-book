# Capítulo 47: autoconf 2.72

## 47.1 — Objetivo

A GNU tool for automatically configuring source code.

## 47.2 — Descargar

```bash
wget https://ftp.gnu.org/gnu/autoconf/autoconf-2.72.tar.xz
```

## 47.3 — Compilar

```bash
cd /sources
tar xvf autoconf-2.72.tar.xz 
cd autoconf-2.72

./configure --prefix=/usr
make

make install
```

## 47.4 — Verificar

```bash
autoconf --version
# autoconf (GNU Autoconf) 2.72

```


---

<< [Anterior](46-curl.md) | [Índice](README.md) | [Siguiente](48-automake.md) >>
