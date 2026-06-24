# Capítulo 38: Sqlite 3.53.2

## 38.1 — Objetivo

A C library that implements an SQL database engine. Optional dependency for Python

## 38.2 — Descargar

```
wget https://sqlite.org/2025/sqlite-autoconf-3510000.tar.gz

```

## 38.3 — Compilar

```
cd /sources
tar xvf sqlite-autoconf-3510000.tar.gz
cd sqlite-autoconf-3510000

sed -i 's/2F 5C/%2F %5C/' scripts/wsqlite

./configure --prefix=/usr     \
            --disable-static  \
            --enable-fts{4,5} \
            CPPFLAGS="-D SQLITE_ENABLE_COLUMN_METADATA=1 \
                      -D SQLITE_ENABLE_UNLOCK_NOTIFY=1   \
                      -D SQLITE_ENABLE_DBSTAT_VTAB=1     \
                      -D SQLITE_SECURE_DELETE=1"
make -j$(nproc) LDFLAGS.rpath=""

make install
```

## 38.4 — Verificar

```
file /lib/libsqlite3.so.3.51.0 
# /lib/libsqlite3.so.3.51.0: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, with debug_info, not stripped

```
