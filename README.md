# Cheat-Sheet-Linux-System-Administrator2

# Dokumentasi Konfigurasi SSH dan Manajemen User pada WSL Ubuntu

## 1. Pendahuluan

Dokumentasi ini mencakup langkah-langkah konfigurasi SSH, manajemen user, serta pembuatan dan pengelolaan file terkait dalam sistem Linux berbasis WSL (Windows Subsystem for Linux) Ubuntu.

# Konfigurasi SSH Server di WSL Ubuntu

## 📌 PENGHANTAR

Sebagai Linux System Administrator, konfigurasi SSH Server sangat penting agar bisa mengakses server dari jarak jauh dengan aman. Proses ini mencakup:

- ✅ Membuat user baru untuk remote login.
- ✅ Mengonfigurasi SSH Server agar hanya bisa diakses dengan key-based authentication.
- ✅ Membuat berkas bukti untuk validasi proyek.
- ✅ Menambahkan fitur tambahan untuk meningkatkan keamanan dan nilai proyek.

---

## 🛠 1. Membuat User Baru

💡 **Tujuan:** Membuat user `dicoding` untuk remote login.

### 1.1 Menambahkan User

```bash
sudo adduser dicoding
```

Isi informasi berikut:

- Password → Masukkan password yang mudah diingat.
- Full Name → `Dicoding Indonesia` (Enter untuk lainnya).

### 1.2 Verifikasi User Berhasil Dibuat

```bash
cat /etc/passwd | grep dicoding
```

✅ **Output yang benar:**

```bash
dicoding:x:1001:1001:Dicoding Indonesia,,,:/home/dicoding:/bin/bash
```

---

## 🛠 2. Mengonfigurasi SSH

💡 **Tujuan:** Mengatur SSH agar bisa remote login dengan aman.

### 2.1 Instal dan Jalankan OpenSSH Server

```bash
sudo apt update && sudo apt install -y openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

### 2.2 Cek Status SSH Server

```bash
sudo systemctl status ssh
```

✅ **Output yang benar:**

```bash
Active: active (running)
```

### 2.3 Remote Login Menggunakan Password (Tes Awal)

```bash
ssh dicoding@localhost
```

Masukkan password user `dicoding`.
✅ Jika berhasil masuk, lanjut ke konfigurasi SSH Key.

### 2.4 Konfigurasi SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub dicoding@localhost
ssh dicoding@localhost
```

✅ Jika bisa login tanpa password, berarti konfigurasi berhasil.

### 2.5 Mengedit Konfigurasi SSH

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

Simpan dan keluar (`CTRL+X`, lalu `Y`, lalu `ENTER`).

### 2.6 Restart SSH Server

```bash
sudo systemctl restart ssh
```

### 2.7 Uji Koneksi dengan Port 2000

```bash
ssh -p 2000 dicoding@localhost
```

✅ Jika bisa login tanpa password, konfigurasi SSH sudah aman.

---

## 🛠 3. Membuat Berkas Bukti

💡 **Tujuan:** Membuat file daftar user dan log SSH sebagai bukti pengerjaan proyek.

### 3.1 Membuat File daftar-user.txt

```bash
cut -d: -f1 /etc/passwd > daftar-user.txt
```

✅ File `daftar-user.txt` berisi daftar user di sistem.

### 3.2 Membuat File log-ssh.txt

```bash
sudo journalctl -u ssh --no-pager > log-ssh.txt
```

✅ File `log-ssh.txt` berisi log SSH.

---

## 🌟 Meningkatkan Penilaian (Fitur Tambahan)

### 4.1 Simpan Log SSH dalam Format JSON

```bash
sudo journalctl -u ssh --no-pager --output=json > log-ssh.json
```

✅ File `log-ssh.json` berisi log dalam format JSON.

### 4.2 Enkripsi File daftar-user.txt

```bash
gpg -c daftar-user.txt
```

Masukkan passphrase: `dicoding`
✅ Hasilnya: `daftar-user.txt.gpg` (file terenkripsi).

### 4.3 Buat Shell Script hapus-log.sh

#### 4.3.1 Buat File Script

```bash
nano hapus-log.sh
```

#### 4.3.2 Tambahkan Skrip Berikut

```bash
#!/bin/bash

while true; do
    echo "=== Sebelum Penghapusan Log ==="
    journalctl --disk-usage
    sudo journalctl --vacuum-size=10M
    echo "=== Setelah Penghapusan Log ==="
    journalctl --disk-usage
    sleep 5
done
```

#### 4.3.3 Simpan dan Jalankan

```bash
chmod +x hapus-log.sh
./hapus-log.sh
```

✅ Script ini akan membersihkan log hingga tersisa 10MB.

---

## 🛠 5. Penanganan Kendala & Solusi

### 5.1 Tidak Bisa Mengakses Folder di Windows

#### **Masalah:**

```bash
ls -ld submission2-linux-panggih_dwi_alamsyah
drwxr-xr-x 2 dicoding dicoding 4096 Mar 11 17:20 submission2-linux-panggih_dwi_alamsyah
```

Tetapi folder tidak muncul di Windows.

#### **Solusi:**

```bash
sudo chmod -R 777 ~/submission2-linux-panggih_dwi_alamsyah
```

Jika masih tidak terdeteksi, coba cek jalur akses di Windows:

```bash
explorer.exe \\wsl$\Ubuntu-24.04\home\dicoding
```

### 5.2 Tidak Bisa Copy Folder ke Windows (Permission Denied)

#### **Masalah:**

```bash
cp -r ~/submission2-linux-panggih_dwi_alamsyah /mnt/c/Users/sawwariorr/
cp: cannot create directory '/mnt/c/Users/sawwariorr/': Permission denied
```

#### **Solusi:**

Jalankan perintah dengan `sudo`:

```bash
sudo cp -r ~/submission2-linux-panggih_dwi_alamsyah /mnt/c/Users/sawwariorr/
```

Pastikan Windows tidak memblokir akses dengan mengubah izin folder:

```bash
sudo chmod -R 777 /mnt/c/Users/sawwariorr/
```

---

## 📌 PENILAIAN

✅ **Kriteria 1 (Membuat User Baru)** → 100% Selesai
✅ **Kriteria 2 (Konfigurasi SSH)** → 100% Selesai
✅ **Kriteria 3 (Membuat Berkas Bukti)** → 100% Selesai
🌟 **Tambahan (JSON log, enkripsi, shell script)** → Nilai Tinggi!

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
