# Capítulo 52: autoconf 2.72

## 52.1 — Objetivo

A GNU tool for automatically configuring source code.

## 52.2 — Descargar

```
wget https://ftp.gnu.org/gnu/autoconf/autoconf-2.72.tar.xz
```

## 52.3 — Compilar

```
cd /sources
tar xvf autoconf-2.72.tar.xz 
cd autoconf-2.72

./configure --prefix=/usr
make

make install
```

## 52.4 — Verificar

```
autoconf --version
# autoconf (GNU Autoconf) 2.72

```
