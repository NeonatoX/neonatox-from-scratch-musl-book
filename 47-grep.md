# Capítulo 47: Grep 3.12 

## 47.1 — Objetivo

Compilar para sustituir el apple de busybox por una version mas completa, requerida por algunos paquetes para compilar

## 47.2 — Descargar

```
wget https://ftpmirror.gnu.org/grep/grep-3.12.tar.xz
```

## 47.3 — Compilar

```
cd /sources
tar xvf grep-3.12.tar.xz 
cd grep-3.12

sed -i "s/echo/#echo/" src/egrep.sh
./configure --prefix=/usr
make -j$(nproc)

make install
```

## 47.4 — Verificar

```
grep --version
# → grep (GNU grep) 3.12

```

