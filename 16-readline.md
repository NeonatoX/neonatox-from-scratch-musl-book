# Capítulo 16: readline

## 16.1 — Objetivo

Compilar readline contra musl. Necesario para bash completo.

## 16.2 — Dependencias

- ncurses (capítulo anterior)

---

## 16.3 — Compilar

```
cd /sources
rm -rf readline-8.3
tar -xf readline-8.3.tar.gz
cd readline-8.3

./configure \
  --prefix=/usr \
  --disable-static \
  --with-curses

make -j$(nproc) SHLIB_LIBS="-lncursesw"
make install 
```

## 16.4 — Verificar

```
ls /usr/lib/libreadline.so*
ls /usr/include/readline/readline.h
```

---

<< [Anterior](15-ncurses.md) | [Índice](README.md) | [Siguiente](17-bash-final.md) >>
