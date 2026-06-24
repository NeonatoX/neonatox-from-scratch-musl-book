# Capítulo 39: Python 3.14.6

## 39.1 — Objetivo

Compilar Python 3.14.6 en `/usr`. Necesario para ninja, meson,
kmod, y muchos scripts del sistema.

**Importante**: Este capítulo asume que GCC ya fue reconstruido con
`--enable-default-pie` (capítulo 27). Si no es así, añadir
`CFLAGS="-fPIE"` y pasar `BLDSHARED="gcc -shared"` en el make.

## 39.2 — Dependencias

- gcc, make (ya en el sistema)
- zlib 1.3.2 (capítulo 30)
- OpenSSL 4.0.1 (capítulo 32)
- libffi 3.5.2 (capítulo 34)
- expat 2.7.1 (capítulo 37)
- sqlite (capítulo 38)

## 39.3 — Descargar

```
wget https://www.python.org/ftp/python/3.14.6/Python-3.14.6.tar.xz
wget https://www.linuxfromscratch.org/patches/lfs/development/Python-3.14.6-openssl_4-1.patch
```

## 39.4 — Compilar

```
cd /sources
rm -rf Python-3.14.6
tar xf Python-3.14.6.tar.xz
cd Python-3.14.6

patch -Np1 -i ../Python-3.14.6-openssl_4-1.patch

./configure --prefix=/usr         \
            --enable-shared       \
            --enable-optimizations \
            --with-system-expat   \
            --with-ensurepip=yes  \
            --docdir=/usr/share/doc/python-3.14.6

make -j$(nproc)
make install
```

## 39.5 — Verificar

```
python3 --version
# → Python 3.14.6

pip3 --version
# → pip 26.1.2 ...

python3 -c "import ctypes; print('ctypes OK')"
# → ctypes OK

python3 -c "import ssl; print('ssl OK')"
# → ssl OK
```

## 39.6 — Notas

- `--enable-shared`: build de `libpython3.14.so` para embeber Python
- `--enable-optimizations`: ejecuta tests para optimizar el intérprete
- `--with-ensurepip=yes`: incluye `pip` en la instalación
- El parche `openssl_4-1` es necesario porque OpenSSL 4.0 eliminó
  funciones como `TLSv1_method()`, `SSLv3_method()`, etc. Sin el
  parche, `_ssl` no compila.
- Si GCC no tiene `--enable-default-pie`, usar en su lugar:
  `CFLAGS="-fPIE" LDFLAGS="-pie"` y `BLDSHARED="gcc -shared"`
- Python es necesario antes que PAM porque PAM usa meson
