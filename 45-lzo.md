# Capítulo 45: LZO 2.10

## 45.1 — Objetivo

Compilar LZO 2.10 en `/usr`. Biblioteca de compresión rápida sin pérdida.
Opcional para btrfs-progs (compresión lzo en btrfs).

## 45.2 — Descargar

```
wget https://www.oberhumer.com/opensource/lzo/download/lzo-2.10.tar.gz
```

## 45.3 — Compilar

```
cd /sources
rm -rf lzo-2.10
tar xf lzo-2.10.tar.gz
cd lzo-2.10

./configure --prefix=/usr
make -j$(nproc)
make install
```

## 45.4 — Verificar

```
file /usr/lib/liblzo2.so
# → ELF ... shared object

pkg-config --libs lzo2
# → -llzo2
```
