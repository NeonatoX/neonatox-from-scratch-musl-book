# Capítulo 51: Nhopkg desde git

## 51.1 — Objetivo

Compilar Git 2.54.0 en `/usr`. Sistema de control de versiones
distribuido. Dependencia de nhopkg para gestionar paquetes `.srcnho`
desde repositorios Git.

## 51.2 — Dependencias

- meson y ninja (capítulo 38)
- libarchive (capítulo 39)
- findutils (find) (capítulo 40)
- plocate (updatedb y locate) (capítulo 41)
- gettext (capítulo 42)
- curl (capítulo 46)
- wget (GNU o BusyBox) (capítulo 10)

## 51.3 — Descargar

```bash
git clone https://github.com/cargabsj175/neonatox-nhopkg.git
```

## 51.4 — Compilar

```bash
    cd /sources
    cd neonatox-nhopkg/
    meson setup build
    
    cd build
    ninja
    ninja install   
    mkdir -p ${LFS}/var/nhopkg/{cache,files,logs,packages,repo}
```

## 51.4 — Verificar

```bash
nhopkg --version

# Nhopkg version 0.5.1

```



---

<< [Anterior](50-git.md) | [Índice](README.md) | [Siguiente](52-util-linux.md) >>
