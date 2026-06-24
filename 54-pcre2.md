# Capítulo 54: pcre2 10.47

## 54.1 — Objetivo

A GNU tool for automatically creating Makefiles.

## 54.2 — Descargar

```
wget https://github.com/PCRE2Project/pcre2/releases/download/pcre2-10.47/pcre2-10.47.tar.bz2
```

## 54.3 — Compilar

```
cd /sources
tar xvf pcre2-10.47.tar.bz2 
cd pcre2-10.47

./configure --prefix=/usr                       \
            --docdir=/usr/share/doc/pcre2-10.47 \
            --enable-unicode                    \
            --enable-jit                        \
            --enable-pcre2-16                   \
            --enable-pcre2-32                   \
            --enable-pcre2grep-libz             \
            --enable-pcre2test-libreadline      \
            --disable-static
make -j$(nproc)

make install
```

## 54.4 — Verificar

```
pcre2-config --version
# 10.47

```
