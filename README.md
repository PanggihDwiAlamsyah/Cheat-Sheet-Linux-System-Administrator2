# Cheat-Sheet-Linux-System-Administrator2

# Dokumentasi Konfigurasi SSH dan Manajemen User pada WSL Ubuntu

## 1. Pendahuluan

Dokumentasi ini mencakup langkah-langkah konfigurasi SSH, manajemen user, serta pembuatan dan pengelolaan file terkait dalam sistem Linux berbasis WSL (Windows Subsystem for Linux) Ubuntu.

## 2. Persiapan Awal

### 2.1 Memeriksa Versi WSL

```bash
wsl --list --verbose
```

Pastikan WSL sudah berjalan dengan benar.

### 2.2 Mengupdate dan Menginstal Paket yang Dibutuhkan

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install openssh-server gnupg
```

## 3. Manajemen User dan Hak Akses

### 3.1 Membuat User Baru

```bash
sudo adduser dicoding
```

Ikuti instruksi untuk mengatur password dan informasi lainnya.

### 3.2 Memberikan Hak Akses Sudo ke User Baru

```bash
sudo usermod -aG sudo dicoding
```

Pastikan user memiliki hak akses sudo.

## 4. Konfigurasi SSH

### 4.1 Mengedit Konfigurasi SSH

Buka file konfigurasi:

```bash
sudo nano /etc/ssh/sshd_config
```

Ubah atau tambahkan baris berikut:

```ini
Port 2000
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Simpan perubahan dan keluar.

### 4.2 Membuat SSH Key Pair

Pada mesin pertama:

```bash
ssh-keygen -t rsa -b 4096
```

Salin kunci publik ke mesin kedua:

```bash
ssh-copy-id -p 2000 dicoding@localhost
```

### 4.3 Restart SSH Service

```bash
sudo systemctl restart ssh
```

### 4.4 Menguji Koneksi SSH

```bash
ssh -p 2000 dicoding@localhost
```

Jika berhasil, user dapat masuk tanpa password.

## 5. Pembuatan dan Pengelolaan File

### 5.1 Membuat Berkas Daftar User

```bash
cut -d: -f1 /etc/passwd > daftar-user.txt
```

### 5.2 Mengambil Log SSH

```bash
sudo journalctl -u ssh --no-pager > log-ssh.txt
```

### 5.3 Menyimpan Log SSH dalam Format JSON

```bash
sudo journalctl -u ssh --output=json-pretty > log-ssh.json
```

### 5.4 Mengenkripsi Berkas User

```bash
gpg -c daftar-user.txt
```

Masukkan password untuk enkripsi.

### 5.5 Membuat Script Pengelolaan Log

Buat file `hapus-log.sh`:

```bash
#!/bin/bash
while true; do
  journalctl --disk-usage
  sudo journalctl --vacuum-size=10M
  journalctl --disk-usage
  sleep 5
done
```

Berikan izin eksekusi:

```bash
chmod +x hapus-log.sh
```

## 6. Mengatur Direktori Submission

### 6.1 Membuat Folder Submission

```bash
mkdir submission2-linux-panggih_dwi_alamsyah
```

### 6.2 Menyalin Berkas ke Folder Submission

```bash
cp daftar-user.txt daftar-user.txt.gpg log-ssh.txt log-ssh.json hapus-log.sh submission2-linux-panggih_dwi_alamsyah/
cp /etc/ssh/sshd_config submission2-linux-panggih_dwi_alamsyah/
```

### 6.3 Mengarsipkan Folder

```bash
zip -r submission2-linux-panggih_dwi_alamsyah.zip submission2-linux-panggih_dwi_alamsyah
```

## 7. Catatan untuk Reviewer

- Password enkripsi file: `dicoding`
- Semua langkah telah dilakukan dan dicek ulang.

## 8. Kesimpulan

Dokumentasi ini mencakup seluruh proses konfigurasi SSH, manajemen user, pembuatan file, dan pengarsipan untuk submission. Semua langkah telah diuji dan berjalan dengan baik.
