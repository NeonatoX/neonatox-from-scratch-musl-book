# Capítulo 22: musl — Rebuild Nativo (con binutils nuevo)

## 22.1 — Objetivo

Recompilar musl 1.2.6 dentro del chroot. Debe hacerse **después** de
reconstruir binutils (capítulo 23), para que musl se enlace contra
la versión correcta de libbfd/libiberty. No hay cambios de
configuración — solo rebuild para mantener coherencia con el resto
de la toolchain.

## 22.2 — Compilar

```
cd /sources
rm -rf musl-1.2.6
tar -xf musl-1.2.6.tar.gz
cd musl-1.2.6

./configure --prefix=/usr

make -j$(nproc)
make install
```

## 22.3 — Verificar

```
/usr/lib/libc.so
# → debe mostrar el banner de musl 1.2.6

gcc -o /tmp/test /tmp/test.c && /tmp/test && echo OK
# → debe funcionar (PIE por defecto de GCC)
```
