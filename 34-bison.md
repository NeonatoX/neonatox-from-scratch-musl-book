# Capítulo 34: bison 3.8.2

## 34.1 — Objetivo

Compilar bison 3.8.2 en `/usr`. Generador de analizadores sintácticos
(yacc). Necesario para Linux-PAM y otros paquetes.

## 34.2 — Dependencias

- gcc, make (ya en el sistema)
- flex (capítulo 35) — opcional pero recomendado

## 34.3 — Descargar

```bash
wget https://ftp.gnu.org/gnu/bison/bison-3.8.2.tar.xz
```

## 34.4 — Compilar

```bash
cd /sources
tar xf bison-3.8.2.tar.xz
cd bison-3.8.2

./configure --prefix=/usr \
            --docdir=/usr/share/doc/bison
make -j$(nproc)

make install
```

## 34.5 — Verificar

```bash
bison --version
# → bison (GNU Bison) 3.8.2
```

## 34.6 — Notas

- bison reemplaza a yacc. Se instala también como `yacc` por defecto
- En musl, bison compila sin problemas
- Necesario para Linux-PAM (parsea gramáticas de configuración)


---

<< [Anterior](33-flex.md) | [Índice](README.md) | [Siguiente](35-expat.md) >>
