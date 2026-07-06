# Capítulo 50: Git 2.54.0

## 50.1 — Objetivo

Compilar Git 2.54.0 en `/usr`. Sistema de control de versiones
distribuido. Dependencia de nhopkg para gestionar paquetes `.srcnho`
desde repositorios Git.

## 50.2 — Dependencias

- gcc, make (ya en el sistema)
- zlib 1.3.2 (capítulo 24)
- OpenSSL 4.0.1 (capítulo 30)
- expat 2.7.1 (capítulo 35)
- Perl 5.40.0 (capítulo 29)
- pcre2 10.50 (opcional, para `git grep` con regex PCRE)

## 50.3 — Descargar

```bash
wget https://www.kernel.org/pub/software/scm/git/git-2.54.0.tar.xz
```

## 50.4 — Compilar

```bash
cd /sources
rm -rf git-2.54.0
tar xf git-2.54.0.tar.xz
cd git-2.54.0

./configure --prefix=/usr                   \
            --with-gitconfig=/etc/gitconfig \
            --with-python=python3           \
            --with-libpcre2 

make -j$(nproc)
make install
```

## 50.5 — Verificar

```bash
git --version
# → git version 2.54.0

git config --global user.name "root"
git config --global user.email "root@localhost"
git init /tmp/test-git && echo "test" > /tmp/test-git/hola.txt && cd /tmp/test-git && git add . && git commit -m "init"
# → [main (root-commit) ...]
```

## 50.6 — Notas

- Python se usa para herramientas auxiliares como `git p4` (Perforce)
  y `git svn`; al estar instalado, configure lo detecta automáticamente.
- Git en musl funciona sin problemas — las operaciones de red usan
  OpenSSL o SSH.
- Para usar git over SSH se necesita un cliente SSH (dropbear o
  openssh).


---

<< [Anterior](49-pcre2.md) | [Índice](README.md) | [Siguiente](51-nhopkg.md) >>
