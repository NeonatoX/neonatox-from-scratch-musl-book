# Capítulo 56: Packaging 26.2

## 56.1 — Objetivo

This includes utilities for version handling, specifiers, markers, tags, and requirements. 

## 56.2 — Descargar

```
wget https://files.pythonhosted.org/packages/source/p/packaging/packaging-26.2.tar.gz

```

## 56.3 — Compilar

```
cd /sources
tar xvf packaging-26.2.tar.gz
cd packaging-26.2


pip3 wheel -w dist --no-cache-dir --no-build-isolation --no-deps $PWD

pip3 install --no-index --find-links dist --ignore-installed --force-reinstall packaging

```

