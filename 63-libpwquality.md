# Capítulo 63: libpwquality 1.4.5

# 63.1 - Objetivo

Password Checking Library.

## 63.3 — Descargar

```bash
wget https://github.com/libpwquality/libpwquality/releases/download/libpwquality-1.4.5/libpwquality-1.4.5.tar.bz2
```

## 63.4 — Compilar

```bash
cd /sources
rm -rf libpwquality-1.4.5
tar -xf libpwquality-1.4.5.tar.bz2
cd libpwquality-1.4.5

/configure \
		--prefix=/usr \
		--sysconfdir=/etc \
		--mandir=/usr/share/man \
      --with-securedir=/usr/lib/security \
		--localstatedir=/var \
		--enable-python-bindings \
		--with-python-binary=python3 \
		--enable-pam
make

make install


mv /etc/pam.d/system-password{,.orig} &&
cat > /etc/pam.d/system-password << "EOF"
# Begin /etc/pam.d/system-password

# check new passwords for strength (man pam_pwquality)
password  required    pam_pwquality.so   authtok_type=UNIX retry=1 difok=1 \
                                         minlen=8 dcredit=0 ucredit=0      \
                                         lcredit=0 ocredit=0 minclass=1    \
                                         maxrepeat=0 maxsequence=0         \
                                         maxclassrepeat=0 gecoscheck=0     \
                                         dictcheck=1 usercheck=1           \
                                         enforcing=1 badwords=""           \
                                         dictpath=/usr/lib/cracklib/pw_dict

# use yescrypt hash for encryption, use shadow, and try to use any
# previously defined authentication token (chosen password) set by any
# prior module.
password  required    pam_unix.so        yescrypt shadow try_first_pass

# End /etc/pam.d/system-password
EOF

```


---

<< [Anterior](62-setuptools.md) | [Índice](README.md) | [Siguiente](64-attr.md) >>
