# Capítulo 58: setuptools 82.0.1

## 58.1 — Objetivo

Setuptools is a tool used to download, build, install, upgrade, and uninstall Python packages. 
 
## 58.2 — Descargar

```
wget https://pypi.org/packages/source/s/setuptools/setuptools-82.0.1.tar.gz

```

## 58.3 — Compilar

```
cd /sources
tar xvf setuptools-82.0.1.tar.gz
cd setuptools-82.0.1


pip3 wheel -w dist --no-cache-dir --no-build-isolation --no-deps $PWD

pip3 install --no-index --find-links dist --ignore-installed --force-reinstall setuptools

```

