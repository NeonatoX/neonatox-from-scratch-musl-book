# Capítulo 52: Git 2.49.0

## 52.1 — Objetivo

Compilar Git 2.49.0 en `/usr`. Sistema de control de versiones
distribuido. Dependencia de nhopkg para gestionar recetas `.srcnho`
desde repositorios Git.

## 52.2 — Dependencias

- gcc, make (ya en el sistema)
- zlib 1.3.2 (capítulo 30)
- OpenSSL 4.0.1 (capítulo 32)
- expat 2.7.1 (capítulo 37)
- Perl 5.40.0 (capítulo 31)
- pcre2 10.52 (opcional, para `git grep` con regex PCRE)

## 52.3 — Descargar

```
wget https://www.kernel.org/pub/software/scm/git/git-2.49.0.tar.xz
```

## 52.4 — Compilar

```
cd /sources
rm -rf git-2.49.0
tar xf git-2.49.0.tar.xz
cd git-2.49.0

./configure --prefix=/usr                   \
            --with-gitconfig=/etc/gitconfig \
            --with-python=python3           \
            --with-libpcre2 

make -j$(nproc)
make install
```

## 52.5 — Verificar

```
git --version
# → git version 2.49.0

git config --global user.name "root"
git config --global user.email "root@localhost"
git init /tmp/test-git && echo "test" > /tmp/test-git/hola.txt && cd /tmp/test-git && git add . && git commit -m "init"
# → [main (root-commit) ...]
```

## 52.6 — Notas

- `--without-tcltk`: evita dependencia de Tcl/Tk (gitk, git-gui).
- Python se usa para herramientas auxiliares como `git p4` (Perforce)
  y `git svn`; al estar instalado, configure lo detecta automáticamente.
- Git en musl funciona sin problemas — las operaciones de red usan
  OpenSSL o SSH.
- Para usar git over SSH se necesita un cliente SSH (dropbear o
  openssh).
