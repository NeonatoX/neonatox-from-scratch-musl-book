# Capítulo 43: Grep 3.12 

## 43.1 — Objetivo

Compilar para sustituir el applet de busybox por una version mas completa, requerida por algunos paquetes para compilar

## 43.2 — Descargar

```bash
wget https://ftpmirror.gnu.org/grep/grep-3.12.tar.xz
```

## 43.3 — Compilar

```bash
cd /sources
tar xvf grep-3.12.tar.xz 
cd grep-3.12

sed -i "s/echo/#echo/" src/egrep.sh
./configure --prefix=/usr
make -j$(nproc)

make install
```

## 43.4 — Verificar

```bash
grep --version
# → grep (GNU grep) 3.12

```



---

<< [Anterior](42-gettext.md) | [Índice](README.md) | [Siguiente](44-libpsl.md) >>
