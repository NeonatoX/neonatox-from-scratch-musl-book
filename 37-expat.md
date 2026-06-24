# Capítulo 37: expat 2.7.1

## 37.1 — Objetivo

Compilar expat 2.7.1 en `/usr`. Parser XML. Necesario para Python
(módulo `pyexpat`).

## 37.2 — Dependencias

- gcc, make (ya en el sistema)

## 37.3 — Descargar

```
wget https://github.com/libexpat/libexpat/releases/download/R_2_7_1/expat-2.7.1.tar.xz
```

## 37.4 — Compilar

```bash
cd /sources
tar xf expat-2.7.1.tar.xz
cd expat-2.7.1

./configure --prefix=/usr --disable-static

make
make install
```

## 37.5 — Verificar

```bash
ldd /usr/lib/libexpat.so
# → libc.so => /lib/ld-musl-x86_64.so.1
```

## 37.6 — Notas

- expat es un parser XML stream-oriented, minimalista
- `--disable-static` ahorra espacio
- Python lo usa para `pyexpat` y `xml.parsers.expat`
- Si no está, Python compila con su copia interna; es mejor usar la
  del sistema para mantener consistencia
