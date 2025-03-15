# Dokumentasi AppScript Blog

## Pendahuluan
AppScript Blog adalah sebuah aplikasi berbasis **Google Apps Script** yang digunakan untuk mengelola data artikel yang disimpan dalam **Google Spreadsheet**. Aplikasi ini memungkinkan pengguna untuk:
- Mengambil semua artikel dari spreadsheet
- Mencari artikel berdasarkan **ID**
- Menampilkan artikel dalam format HTML
- Menggunakan template untuk menampilkan data secara dinamis

## Struktur Proyek
Proyek ini terdiri dari beberapa kelas utama:

### 1. Kelas `Article`
Merepresentasikan sebuah objek artikel.
```javascript
class Article {
  constructor(id, title, content, image, meta, author) {
    this.id = id;
    this.title = title;
    this.content = content;
    this.image = image;
    this.meta = meta;
    this.author = author;
  }
}
```

### 2. Kelas `ArticleManager`
Bertanggung jawab untuk mengelola data artikel dari Google Spreadsheet.

```javascript
class ArticleManager {
  constructor(sheetId, sheetName) {
    this.sheet = SpreadsheetApp.openById(sheetId).getSheetByName(sheetName);
  }

  getAllArticles() {
    const data = this.sheet.getDataRange().getValues();
    const articles = [];
    
    for (let i = 1; i < data.length; i++) {
      articles.push(
        new Article(
          data[i][0], // ID
          data[i][1], // Title
          data[i][2], // Content
          data[i][3], // Image URL
          data[i][4], // Meta
          data[i][5]  // Author
        )
      );
    }

    return articles;
  }

  getArticleById(id) {
    const data = this.sheet.getDataRange().getValues();
    
    for (let i = 1; i < data.length; i++) {
      if (data[i][0] == id) {
        return new Article(
          data[i][0],
          data[i][1],
          data[i][2],
          data[i][3],
          data[i][4],
          data[i][5]
        );
      }
    }
    return { error: true, message: "Artikel tidak ditemukan" };
  }
}
```

### 3. Kelas `App`
Mengelola logika utama aplikasi.
```javascript
class App {
  constructor(sheetId, sheetName) {
    this.articleManager = new ArticleManager(sheetId, sheetName);
  }

  doGet(e) {
    let page = e.parameter.page || "mainPage";
    let output = HtmlService.createTemplateFromFile(page);
    output.jsonData = this.articleManager.getAllArticles();

    if (page === "artikel") {
      const id = e.parameter.id;
      output.artikel = this.articleManager.getArticleById(id);
    }

    return output
      .evaluate()
      .addMetaTag("viewport", "width=device-width, initial-scale=1")
      .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
  }
}
```

### 4. Fungsi Global
Fungsi yang digunakan sebagai entry point dalam **Google Apps Script**.
```javascript
const app = new App("<SHEET_ID>", "Artikel");

function doGet(e) {
  return app.doGet(e);
}

function include(filename) {
  return app.include(filename);
}

function myURL() {
  return app.myURL();
}
```

## Struktur Spreadsheet
Spreadsheet yang digunakan memiliki format berikut:

| ID | Title | Content | Image | Meta | Author |
|----|-------|---------|-------|------|--------|
| 1  | Contoh Judul | <div>Isi artikel</div> | URL Gambar | Deskripsi singkat | Nama Penulis |

## Tampilan HTML
Aplikasi ini menggunakan template HTML untuk menampilkan artikel:
```html
<?!= include('header'); ?>
<div class="col-md-8">
  <h3>Berita Terbaru</h3>
  <? for (var i = 0; i < jsonData.length; i++) { ?>
    <article>
      <h2><a href="?page=artikel&id=<?= jsonData[i].id ?>"><?= jsonData[i].title ?></a></h2>
      <img src="<?= jsonData[i].image ?>" alt="<?= jsonData[i].title ?>">
      <p><?= jsonData[i].meta ?></p>
    </article>
  <? } ?>
</div>
<?!= include('footer'); ?>
```

## Cara Deploy
1. **Buka Google Apps Script** di Google Drive.
2. **Tambahkan kode di editor**.
3. **Hubungkan dengan Google Spreadsheet** dengan memasukkan `SHEET_ID`.
4. **Simpan dan jalankan fungsi `doGet`** untuk melihat hasilnya.
5. **Deploy sebagai Web App** melalui menu **Deploy > New Deployment**.

## Kesimpulan
AppScript Blog adalah aplikasi sederhana untuk mengelola artikel berbasis **Google Sheets dan Apps Script**. Dengan struktur **OOP (Object-Oriented Programming)**, aplikasi ini mudah dikembangkan dan digunakan untuk kebutuhan blog sederhana.
