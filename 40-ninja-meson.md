# Capítulo 40: ninja 1.13.2 + meson 1.11.1

## 40.1 — Objetivo

Instalar ninja y meson vía pip. Necesarios para Linux-PAM y kmod.

## 40.2 — Dependencias

- Python 3.14.6 (capítulo 37) con pip

## 40.3 — Instalar

```bash
pip3 install ninja meson
```

## 40.4 — Verificar

```bash
ninja --version
# → 1.13.2

meson --version
# → 1.11.1
```

## 40.5 — Notas

- Se instalan vía `pip3` (incluido con `--with-ensurepip=yes`)
- Los binarios quedan en `/usr/bin/`
- meson necesita ninja para compilar; pip instala ambas dependencias
- No compilar ninja desde fuente; es más rápido vía pip
