# Capítulo 25: Gawk-5.4.0

## 25.1 — Objetivo

### Nota
Aunque BusyBox incluye una implementación de awk, para construir el GCC final se requiere GNU Awk. Durante el desarrollo de NeonatoX se comprobó que BusyBox awk no produce un compilador completamente funcional en esta etapa. Instale este paquete antes de continuar con el capítulo 27 - GCC Final.

## 25.2 — Dependencias

- make, gcc (STAGE 2) (ya en el sistema)


## 25.3 — Descargar

```
wget https://ftpmirror.gnu.org/gawk/gawk-5.4.0.tar.xz
```

## 25.4 — Compilar

```bash
cd /sources
tar xf gawk-5.4.0.tar.xz
cd gawk-5.4.0

sed -i 's/extras//' Makefile.in

./configure --prefix=/usr
make -j$(nproc)

rm -f /usr/bin/gawk-5.4.0
make install
ln -sf gawk /usr/bin/awk
```

---

<< [Anterior](24-zlib.md) | [Índice](README.md) | [Siguiente](26-sed.md) >>
