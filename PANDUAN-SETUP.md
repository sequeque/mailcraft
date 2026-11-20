# MailCraft: panduan go-live & testing Gmail

Folder ini adalah website statis yang siap di-upload. Tidak perlu server, database, atau build.

```
index.html     aplikasi MailCraft
config.js      tempat Google Client ID (satu-satunya yang perlu diisi)
privacy.html   Kebijakan Privasi (diperlukan di layar izin Google)
terms.html     Ketentuan
```

## 1. Upload ke hosting

Upload isi folder ini apa adanya ke hosting statis mana pun (Netlify, Vercel, GitHub Pages, cPanel, dan sebagainya).
Domain yang dipakai untuk testing **sama dengan** domain live. Tidak ada link terpisah.

> Login Google tidak berjalan kalau `index.html` dibuka langsung dari folder (file://).
> Untuk mencoba di komputer sendiri, jalankan `python3 -m http.server 8080` di folder ini, lalu buka http://localhost:8080.

## 2. Siapkan Google Cloud (sekali saja, ±10 menit)

1. Buka https://console.cloud.google.com/projectcreate dan buat project, misalnya "MailCraft".
2. Aktifkan **Gmail API**: https://console.cloud.google.com/apis/library/gmail.googleapis.com
3. Buka **Google Auth Platform**: https://console.cloud.google.com/auth/overview
   - **Branding**: isi nama app, email dukungan, dan link `https://DOMAIN-KAMU/privacy.html`.
   - **Audience**: pilih *External*, status biarkan **Testing**. Tambahkan email Gmail yang boleh mencoba di **Test users** (maksimal 100).
   - **Data Access**: tambahkan scope `https://www.googleapis.com/auth/gmail.send`.
   - **Clients**: buat client tipe **Web application**. Di *Authorized JavaScript origins* isi alamat situs **tanpa garis miring di akhir**, contoh `https://mailcraft-kamu.netlify.app`. Tambahkan juga `http://localhost:8080` kalau mau tes lokal.
4. Salin **Client ID** (berakhiran `.apps.googleusercontent.com`) lalu tempel di `config.js`:
   ```js
   window.MAILCRAFT_CONFIG = { googleClientId: '1234-abc.apps.googleusercontent.com' };
   ```
   Upload ulang `config.js`. Client ID bukan rahasia. **Jangan** pernah menaruh *Client secret* di file mana pun.

Tips: kalau belum sempat edit config.js, di situs live buka **Kirim → Siapkan koneksi Gmail**. Dialog itu menampilkan alamat origin persis yang harus didaftarkan, dan Client ID bisa ditempel di situ untuk mencoba di browser tersebut saja.

## 3. Uji end-to-end

1. Buka situs, klik **Buat Email Sekarang**, lalu pilih template.
2. Di **Preview & tes**, isi **From** dengan akun Gmail mana pun yang mau dipakai mengirim, lalu isi **Email tujuan tes** dengan alamat siapa saja.
3. Klik **Kirim email tes**. Kalau akun From belum terhubung, Google langsung meminta login akun itu.
   - Selama mode Testing akan muncul peringatan "Google hasn't verified this app". Ini normal: klik *Continue*, lalu **centang izin kirim email**.
   - Akun From harus terdaftar sebagai *test user* (langkah 2.3).
   - Kalau kamu login dengan akun yang berbeda dari From, email **tidak** dikirim. From diganti ke akun yang benar-benar login, lalu klik kirim lagi.
4. Di **Kirim**, isi From dan To dengan bebas, satu orang atau beberapa:
   - **✉ Kirim email**: 1 email biasa ke semua alamat di To.
   - **👤 Satu per satu** (muncul jika penerima lebih dari 1): email terpisah yang menyapa tiap orang dengan `{{nama}}`.
   - **Sekaligus (BCC)**: 1 email, penerima tidak saling melihat.
5. Beberapa akun Gmail bisa terhubung sekaligus. Klik **Pakai** untuk berganti From, atau **Tambah akun Gmail lain**.
6. Coba tombol **back browser** di setiap langkah. Refresh halaman tetap di langkah yang sama.

## Catatan penting

- **Batas Gmail**: akun pribadi ±500 penerima/hari, Google Workspace ±2.000/hari. Jeda antar email bisa diatur di Pengaturan (bawaan 2 detik).
- **Sesi login** berlaku ±1 jam. Kalau habis di tengah pengiriman, proses berhenti dengan aman. Klik **Hubungkan ulang**, lalu **Kirim ke sisa** (yang sudah terkirim tidak dikirim lagi).
- **Gambar upload** disimpan di browser dan dikirim sebagai lampiran inline, jadi tampil di Gmail tanpa hosting gambar. Kapasitas penyimpanan browser ±5 MB, jadi hapus gambar lama di "Gambar saya" jika muncul peringatan penuh.
- **Mode Testing** cukup untuk test user yang terdaftar. Agar siapa saja bisa login tanpa peringatan, klik *Publish app* di Audience dan ajukan verifikasi Google. Scope `gmail.send` termasuk *sensitive*, sehingga butuh domain terverifikasi, halaman privasi, dan video demo. Proses ini biasanya memakan beberapa hari sampai minggu.
- Metode **Buka Gmail & tempel** tetap tersedia sebagai cadangan dan tidak butuh setup apa pun.
