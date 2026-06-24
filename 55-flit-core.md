# Capítulo 55: flit-core 3.12.0

## 55.1 — Objetivo

Flit-core is the distribution-building parts of Flit (a packaging tool for simple Python modules). 
 
## 55.2 — Descargar

```
wget https://pypi.org/packages/source/f/flit-core/flit_core-3.12.0.tar.gz

```

## 55.3 — Compilar

```
cd /sources
tar xvf flit_core-3.12.0.tar.gz
cd flit_core-3.12.0

pip3 wheel -w dist --no-cache-dir --no-build-isolation --no-deps $PWD

pip3 install --no-index --find-links dist --ignore-installed --force-reinstall flit_core

```

