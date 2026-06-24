# Capítulo 36: bison 3.8.2

## 36.1 — Objetivo

Compilar bison 3.8.2 en `/usr`. Generador de analizadores sintácticos
(yacc). Necesario para Linux-PAM y otros paquetes.

## 36.2 — Dependencias

- gcc, make (ya en el sistema)
- flex (capítulo 35) — opcional pero recomendado

## 36.3 — Descargar

```
wget https://ftp.gnu.org/gnu/bison/bison-3.8.2.tar.xz
```

## 36.4 — Compilar

```bash
cd /sources
tar xf bison-3.8.2.tar.xz
cd bison-3.8.2

./configure --prefix=/usr \
            --docdir=/usr/share/doc/bison-3.8.2

make
make install
```

## 36.5 — Verificar

```bash
bison --version
# → bison (GNU Bison) 3.8.2
```

## 36.6 — Notas

- bison reemplaza a yacc. Se instala también como `yacc` por defecto
- En musl, bison compila sin problemas
- Necesario para Linux-PAM (parsea gramáticas de configuración)
