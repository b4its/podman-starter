## Learn podman + quadlet with Rust, and Vue.
### structure
```bash
learn/
└── quadlet-files/  <-- Simpan link/file ke sini
    ├── learn.network
    ├── mysql-data.volume
    ├── learn-db.container
    ├── learn-backend.container
    └── learn-frontend.container
```
### untuk menjalankan semua service dari podman
```bash
systemctl --user daemon-reload
systemctl --user start learn-network.service
systemctl --user start mysql-data-volume.service
systemctl --user start learn-db.service
```

```bash
# membuat folder jika belum ada di system
mkdir -p ~/.config/containers/systemd/

# pindahkan dari folder quadlet-files ke system
cp quadlet-files/* ~/.config/containers/systemd/

# untuk membuat systemd membaca konfigurasi quadlet
systemctl --user daemon-reload


# untuk memastikan apakah quadlet berhasil terbaca atau tidak
systemctl --user list-unit-files | grep learn

# untuk melihat status dari service
systemctl --user status [nama-serivce].service

systemctl --user status learn-db.service
systemctl --user status learn-frontend.service
systemctl --user status learn-backend.service
# untuk melihat container yang sedang berjalan
podman ps

# bersihkan container gagal start
podman rm -f learn-db

systemctl --user stop learn-backend.service
rm ~/.config/containers/systemd/learn-backend.container
cp ~/programming/rust/cargoRs/podman/quadlet-files/learn-backend.container ~/.config/containers/systemd/
rm ~/.config/containers/systemd/learn-frontend.container
cp ~/programming/rust/cargoRs/podman/quadlet-files/learn-frontend.container ~/.config/containers/systemd/

systemctl --user cat learn-backend.service | grep ExecStart
systemctl --user cat learn-frontend.service | grep ExecStart

#build manual
podman build -t my-rust-backend backend-rust
```
### menjalankan rust di podman
```bash
podman build -t my-rust-backend backend-rust
systemctl --user daemon-reload
systemctl --user start learn-backend.service
```
### menjalankan vue di podman
```bash
podman build -t vue-frontend frontend-rust
podman build -t localhost/my-vue-frontend:latest ./frontend-vue
systemctl --user daemon-reload
systemctl --user start learn-frontend.service

# audit log
journalctl --user -xeu learn-frontend.service
```

### membuat symlink
```bash
# Hapus link lama di folder systemd
rm -f ~/.config/containers/systemd/learn-frontend.container

# Buat ulang link (pastikan path ke folder project benar)
ln -s ~/programming/rust/cargoRs/podman/quadlet-files/learn-frontend.container ~/.config/containers/systemd/
```