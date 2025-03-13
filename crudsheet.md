
# Dokumentasi Template Manajemen Data Mahasiswa dengan Google Apps Script & Google Sheets

## 1. Pendahuluan

Template **Manajemen Data Mahasiswa** ini memungkinkan pengguna untuk mengelola data mahasiswa secara efektif menggunakan **Google Sheets** sebagai database dan **Google Apps Script** untuk pengolahan data. Dengan antarmuka berbasis **Bootstrap**, pengguna dapat dengan mudah melakukan operasi **CRUD** (Create, Read, Update, Delete) pada data mahasiswa secara dinamis. Template ini sangat cocok untuk digunakan dalam berbagai keperluan administrasi pendidikan, baik untuk dosen, admin akademik, atau modul pembelajaran yang membutuhkan manajemen data mahasiswa.

---

## 2. Fitur Utama

- **CRUD Lengkap**: Menambah, mengedit, dan menghapus data mahasiswa secara langsung melalui UI.
- **Kolom Dinamis**: Kolom dalam spreadsheet dapat disesuaikan tanpa perlu mengubah kode aplikasi. Struktur kolom mengikuti header yang ada pada Google Sheets.
- **Desain Responsif**: Menggunakan Bootstrap untuk antarmuka pengguna yang modern dan mudah digunakan di berbagai perangkat.
- **Modal Dialog untuk Input**: Menyediakan modal dialog untuk menambah dan mengedit data mahasiswa dengan form yang interaktif dan responsif.
- **Data Disimpan di Google Sheets**: Tidak perlu menggunakan database eksternal. Semua data mahasiswa tersimpan di Google Sheets, memudahkan pengelolaan.
- **Tanpa Login**: Aplikasi siap digunakan langsung tanpa perlu sistem login, cocok untuk pengguna internal.

---

## 3. Instalasi

### Langkah 1: Menyiapkan Google Sheets
1. **Buat Spreadsheet Baru** di Google Sheets.
2. **Buat Sheet bernama "Mahasiswa"** dengan kolom-kolom berikut:
   - **ID**: Kolom unik untuk identifikasi setiap mahasiswa.
   - **NIM**: Nomor Induk Mahasiswa.
   - **Nama**: Nama mahasiswa.
   - **Jurusan**: Jurusan yang diambil.
   - **Email**: Alamat email mahasiswa.
   - **Kelas**: Kelas mahasiswa.

   **Contoh:**
   | ID | NIM      | Nama        | Jurusan  | Email             | Kelas  |
   |----|----------|-------------|----------|-------------------|--------|
   | 1  | 12345678 | Ali Ahmad   | Teknik   | ali@example.com   | A      |

### Langkah 2: Menambahkan Script Google Apps
1. Buka **Google Apps Script** dari Google Sheets (Klik `Ekstensi` > `Apps Script`).
2. Buat file **Code.gs** dan salin kode di bawah ini:

   ```javascript
   function getColumns() {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Mahasiswa");
     var columns = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
     return columns;
   }

   function getMahasiswa() {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Mahasiswa");
     var data = sheet.getDataRange().getValues();
     var columns = data[0];
     var mahasiswa = [];

     for (var i = 1; i < data.length; i++) {
       var obj = {};
       for (var j = 0; j < columns.length; j++) {
         obj[columns[j]] = data[i][j];
       }
       mahasiswa.push(obj);
     }
     return mahasiswa;
   }

   function addMahasiswa(nim, nama, jurusan, email, kelas) {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Mahasiswa");
     var newRow = sheet.getLastRow() + 1;
     sheet.getRange(newRow, 1).setValue(newRow - 1);  // ID otomatis
     sheet.getRange(newRow, 2).setValue(nim);
     sheet.getRange(newRow, 3).setValue(nama);
     sheet.getRange(newRow, 4).setValue(jurusan);
     sheet.getRange(newRow, 5).setValue(email);
     sheet.getRange(newRow, 6).setValue(kelas);
   }

   function updateMahasiswa(data) {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Mahasiswa");
     var rows = sheet.getDataRange().getValues();
     for (var i = 1; i < rows.length; i++) {
       if (rows[i][0] == data.id) {
         sheet.getRange(i + 1, 2).setValue(data.nim);
         sheet.getRange(i + 1, 3).setValue(data.nama);
         sheet.getRange(i + 1, 4).setValue(data.jurusan);
         sheet.getRange(i + 1, 5).setValue(data.email);
         sheet.getRange(i + 1, 6).setValue(data.kelas);
       }
     }
   }

   function deleteMahasiswa(id) {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Mahasiswa");
     var rows = sheet.getDataRange().getValues();
     for (var i = 1; i < rows.length; i++) {
       if 
