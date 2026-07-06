# Capítulo : ninja 1.13.2 + meson 1.11.1

## 38.1 — Objetivo

Instalar ninja y meson vía pip. Necesarios para Linux-PAM y kmod.

## 38.2 — Dependencias

- Python 3.14.6 (capítulo 37) con pip

## 38.3 — Instalar

```bash
pip3 install ninja meson
```

## 38.4 — Verificar

```bash
ninja --version
# → 1.13.2

meson --version
# → 1.11.1
```

## 38.5 — Notas

- Se instalan vía `pip3` (incluido con `--with-ensurepip=yes`)
- Los binarios quedan en `/usr/bin/`
- meson necesita ninja para compilar; pip instala ambas dependencias
- No compilar ninja desde fuente; es más rápido vía pip


---

<< [Anterior](37-python.md) | [Índice](README.md) | [Siguiente](39-libarchive.md) >>
