# Capítulo 59: Markupsafe 3.0.3

## 59.1 — Objetivo

MarkupSafe is a Python module that implements an XML/HTML/XHTML Markup safe string. 
  
## 59.2 — Descargar

```
wget https://pypi.org/packages/source/M/MarkupSafe/markupsafe-3.0.3.tar.gz

```

## 59.3 — Compilar

```
cd /sources
tar xvf markupsafe-3.0.3.tar.gz
cd markupsafe-3.0.3


pip3 wheel -w dist --no-cache-dir --no-build-isolation --no-deps $PWD

pip3 install --no-index --find-links dist --ignore-installed --force-reinstall Markupsafe

```

