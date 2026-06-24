# Capítulo 57: Wheel 0.46.1

## 57.1 — Objetivo

Wheel is a Python library that is the reference implementation of the Python wheel packaging standard. 

## 57.2 — Descargar

```
wget https://pypi.org/packages/source/w/wheel/wheel-0.46.1.tar.gz

```

## 57.3 — Compilar

```
cd /sources
tar xvf wheel-0.46.1.tar.gz
cd wheel-0.46.1


pip3 wheel -w dist --no-cache-dir --no-build-isolation --no-deps $PWD

pip3 install --no-index --find-links dist --no-deps --ignore-installed --force-reinstall wheel

```

