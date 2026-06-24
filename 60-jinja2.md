# Capítulo 60: Jinja2 3.1.6

## 60.1 — Objetivo

Jinja2 is a Python module that implements a simple pythonic template language. 
  
## 60.2 — Descargar

```
wget https://pypi.org/packages/source/J/Jinja2/jinja2-3.1.6.tar.gz

```

## 60.3 — Compilar

```
cd /sources
tar xvf jinja2-3.1.6.tar.gz
cd jinja2-3.1.6


pip3 wheel -w dist --no-cache-dir --no-build-isolation --no-deps $PWD

pip3 install --no-index --find-links dist --no-deps --ignore-installed --force-reinstall Jinja2

```

