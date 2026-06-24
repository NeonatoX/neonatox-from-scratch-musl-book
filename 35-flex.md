# Capítulo 35: flex 2.6.4

## 35.1 — Objetivo

Compilar flex 2.6.4 en `/usr`. Generador de analizadores léxicos.
Necesario para Linux-PAM y otros paquetes.

## 35.2 — Dependencias

- gcc, make (ya en el sistema)

## 35.3 — Descargar

```
wget https://github.com/westes/flex/releases/download/v2.6.4/flex-2.6.4.tar.gz
```

## 35.4 — Compilar

```bash
cd /sources
tar xf flex-2.6.4.tar.gz
cd flex-2.6.4

./configure --prefix=/usr \
            --docdir=/usr/share/doc/flex-2.6.4 \
            --disable-static

make
make install

ln -sv flex /usr/bin/lex
```

## 35.5 — Verificar

```bash
flex --version
# → flex 2.6.4
```

## 35.6 — Notas

- flex reemplaza a lex. Se crea symlink `lex → flex` para compatibilidad
- En musl, flex compila sin problemas
- Necesario para Linux-PAM (parsea archivos de configuración)
