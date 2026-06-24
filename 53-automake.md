# Capítulo 53: Automake 1.18.1

## 53.1 — Objetivo

A GNU tool for automatically creating Makefiles.

## 53.2 — Descargar

```
wget https://ftp.gnu.org/gnu/automake/automake-1.18.1.tar.xz
```

## 53.3 — Compilar

```
cd /sources
tar xvf automake-1.18.1.tar.xz 
cd automake-1.18.1

./configure --prefix=/usr --docdir=/usr/share/doc/automake
make

make install
```

## 53.4 — Verificar

```
automake --version
# automake (GNU Automake) 1.18.1

```
