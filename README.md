# Spark Package Tools (SPT)

Repository resmi package untuk **Spark OS** — sistem operasi Linux minimal berbasis BusyBox.

Package di sini diinstall melalui perintah `spt` yang sudah tersedia di dalam Spark OS.

---

## Cara Pakai (dari dalam Spark OS)

```sh
spt update              # Perbarui daftar package
spt install <nama>      # Install package
spt remove <nama>       # Hapus package
spt list                # Tampilkan semua package tersedia
spt search <kata>       # Cari package
spt info <nama>         # Lihat detail package
```

---

## Package Tersedia

| Package | Versi | Arsitektur | Keterangan |
|---------|-------|------------|-----------|
| `bash` | 5.2.15 | x86_64, aarch64 | GNU Bash shell |
| `nano` | 2.0.15 | x86_64, aarch64 | Text editor terminal |
| `curl` | 8.19.0 | x86_64, aarch64 | HTTP/HTTPS client (static) |
| `jq` | 1.8.1 | x86_64, aarch64 | JSON processor |
| `btm` | 0.12.3 | x86_64, aarch64 | System monitor (alternatif htop) |
| `tree` | 2.1.1 | x86_64, aarch64 | Directory tree viewer dengan warna |
| `file` | 5.45 | x86_64, aarch64 | File type identification tool |
| `tmux` | 3.4 | x86_64, aarch64 | Terminal multiplexer |

---

## Struktur Repo

```
Spark-Package-Tools/
├── x86_64/
│   ├── PACKAGES              # Index package untuk x86_64
│   ├── bash-5.2.15.spk
│   ├── nano-2.0.15.spk
│   └── ...
├── aarch64/
│   ├── PACKAGES              # Index package untuk aarch64
│   ├── bash-5.2.15.spk
│   └── ...
└── CNAME                     # Domain: spt.sparkos.eu.cc
```

---

## Format Package (.spk)

File `.spk` adalah archive `tar.gz` dengan struktur berikut:

```
nama-versi.spk
├── PKGINFO          # Metadata package
├── files/           # File yang akan diinstall ke rootfs
│   └── usr/bin/nama
└── scripts/
    └── post-install.sh   # Script dijalankan setelah install (opsional)
```

### Isi PKGINFO

```
name=nama-package
version=1.0.0
arch=x86_64
description=Deskripsi singkat package
depends=
maintainer=NamaKamu
size=123456
build_date=2026-01-01
```

### Isi PACKAGES (index)

Setiap package didaftarkan di file `PACKAGES` dengan format:

```
Package: nama
Version: 1.0.0
Arch: x86_64
Description: Deskripsi singkat
Maintainer: NamaKamu
BuildDate: 2026-01-01
Size: 123456
Filename: x86_64/nama-1.0.0.spk

```

> Pisahkan antar package dengan satu baris kosong.

---

## Kontribusi Package

Repo ini **open source** — siapapun boleh menambahkan package baru.

### Syarat Package

- Binary harus **static** atau **self-contained** (tidak bergantung pada library eksternal yang belum ada di Spark OS)
- Ukuran wajar — hindari package yang terlalu besar tanpa alasan jelas
- Sudah ditest berjalan di dalam Spark OS (chroot, proot, atau ISO)
- Satu package = satu commit yang rapi

### Cara Kontribusi

1. **Fork** repo ini
2. Buat file `.spk` untuk package baru:

```sh
# Contoh struktur untuk package "htop"
mkdir -p htop-3.3.0/{files/usr/bin,scripts}

# Taruh binary di files/
cp /path/to/htop htop-3.3.0/files/usr/bin/htop

# Buat PKGINFO
cat > htop-3.3.0/PKGINFO << EOF
name=htop
version=3.3.0
arch=x86_64
description=Interactive process viewer
depends=
maintainer=NamaKamu
size=$(stat -c%s htop-3.3.0/files/usr/bin/htop)
build_date=$(date +%Y-%m-%d)
EOF

# Buat post-install.sh (opsional)
echo '#!/bin/sh' > htop-3.3.0/scripts/post-install.sh

# Pack jadi .spk
tar czf x86_64/htop-3.3.0.spk -C htop-3.3.0 .
```

3. Daftarkan di `x86_64/PACKAGES` dan/atau `aarch64/PACKAGES`
4. **Commit** dan buat **Pull Request**
5. Kalau package bagus dan berfungsi, akan di-merge

### Tips

- Cari binary static di [musl.cc](https://musl.cc) atau build sendiri dengan `--static`
- Test dulu di QEMU sebelum PR: `spt install /path/to/package.spk`
- Untuk aarch64, binary harus dikompile untuk ARM64 (bisa dari Termux atau cross-compile)

---

## Kontak

- Spark OS: [github.com/Shannzx10/Spark-Os](https://github.com/Shannzx10/Spark-Os)
- Maintainer: [@Shannzx10](https://github.com/Shannzx10)
