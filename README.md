Vynime adalah situs katalog dan pemutar video Anime/Film statis. Proyek ini memakai HTML, CSS, dan JavaScript biasa; tidak ada framework, paket npm, maupun proses build.

## Mulai

- Buka `index.html` di browser. Jika browser membatasi `localStorage` pada file lokal, jalankan folder ini lewat server statis lokal.
- File aset dimuat langsung dari folder proyek. `index.html` memakai versi query `?v=4` pada CSS dan JavaScript untuk meminta browser mengambil aset terbaru; naikkan angkanya setelah perubahan besar bila browser masih menampilkan versi cache.

## Peta file

- `index.html` — kerangka halaman: header dan pencarian, sidebar, navigasi kategori, grid galeri, tampilan pemutar, serta markup skeleton saat awal load.
- `anime.css` — tema gelap, layout desktop/mobile, kartu thumbnail, tombol hapus, tampilan pemutar, dan animasi skeleton shimmer.
- `anime.js` — data katalog dan seluruh perilaku interaktif: filter, pencarian, kartu, pemutaran episode, riwayat tontonan, dan menu mobile.
- `poster/` — poster katalog dan gambar episode. Jalur di data relatif terhadap root proyek.
- `.gitignore` — mengabaikan file Markdown secara umum; `README.md` dikecualikan agar catatan ini dapat masuk Git.

## Cara kerja halaman

### Galeri, kategori, dan pencarian

Katalog berada di array `animeVideos` di bagian atas `anime.js`. Kategori awal adalah Anime. Tombol Anime/Film mengatur `activeCategory`; tombol **telah ditonton** mengganti galeri ke koleksi riwayat dan menyembunyikan navigasi kategori. Memilih Anime atau Film lagi mengembalikan navigasi itu.

`readyVideos()` hanya mengambil entri yang judulnya tidak kosong. Ketika kotak pencarian kosong, galeri menampilkan paling banyak 13 entri yang cocok dengan kategori. Saat ada teks pencarian, pencarian memeriksa semua entri berjudul, termasuk yang berada setelah 13 kartu awal. Kartu di luar hasil yang sedang ditampilkan tidak dibuat di DOM, sehingga browser tidak meminta poster kartunya. Gambar kartu memakai `loading="lazy"`.

Pencarian mencocokkan judul dan nama channel. Entri dengan `title: ""` tidak terlihat dan belum bisa ditemukan sampai judul diisi.

### Menambahkan Anime atau Film

Tambahkan objek baru ke array `animeVideos` di `anime.js`. Bentuk dasarnya:

```js
{
  title: "Judul",
  channel: "Anime",
  category: "Anime", // isi "Film" untuk kategori Film
  poster: "poster/judul.jpg",
  episodes: [
    {
      title: "Episode 1", // untuk film bisa dinamai "Full Movie"
      poster: "poster/judul/episode-01.jpg",
      source: "https://contoh/alamat-video.mp4"
    }
  ]
},
```

Untuk seri, masukkan satu objek per episode ke `episodes`. Untuk film, satu item episode sudah cukup. `poster` adalah poster kartu; `episodes[].poster` ada di struktur data untuk poster episode, sementara daftar episode berikutnya saat ini memakai poster utama anime. Sumber video perlu berupa URL atau path yang bisa dimuat elemen `<video>` browser.

Ada slot kosong di bagian akhir array. Isi `title`, `poster`, kategori, serta alamat sumbernya. Pastikan path gambar benar dan nama file cocok dengan kapitalisasi. Jangan lupa koma antarobjek array.

### Pemutaran

Klik kartu akan membuka `watchView` dan memutar episode pertama yang mempunyai judul dan sumber tidak kosong. Panel **Video selanjutnya** dibangun dari episode valid milik seri yang sama. Tombol Kembali hanya menukar tampilan player dan galeri di halaman yang sama; tombol brand menuju `index.html` sehingga browser memuat ulang halaman.

### Koleksi telah ditonton

Anime/film dicatat ketika video benar-benar mengeluarkan event `play`. Data disimpan di `localStorage` browser dengan nama key `vynime-watched-videos`, jadi bertahan setelah reload di browser/perangkat yang sama. Penghapusan data situs/browser atau memakai browser/perangkat lain tidak berbagi riwayat itu.

Kunci riwayat menyertakan kategori, judul, dan poster agar entri Anime dan Film yang memakai thumbnail sama tidak otomatis dianggap sama. Kode juga memigrasikan format lama; untuk data lama yang ambigu, entri Anime dipilih. Di halaman koleksi, tombol × merah di pojok thumbnail menghapus entri dari riwayat dan `localStorage`.

## HTML, CSS, dan JavaScript

### HTML

`index.html` memakai elemen semantik utama: `header`, `aside`, `main`, `nav`, `section`, dan `video`. ID penting yang dipakai JavaScript adalah `videoGrid`, `categoryNav`, `emptyState`, `galleryView`, `watchView`, `watchVideo`, `backToGallery`, `nextVideoList`, `watchedLink`, dan `menuToggle`. Jika ID diganti di HTML, perbarui pencarian elemen terkait di `anime.js` juga.

Grid berisi 13 skeleton awal yang dibuat saat HTML dibaca. Setelah script siap, `anime.js` menggantinya dengan kartu setelah jeda singkat. Jangan menghapus markup/script skeleton tanpa memindahkan tanggung jawab itu ke tempat lain.

### CSS

`anime.css` memakai variabel warna dan ukuran di `:root`. Header berada tetap di atas; sidebar tetap di sisi kiri pada layar lebar dan menjadi menu geser pada layar kecil. `.video-grid` memakai grid responsif, `.thumbnail-wrap` menjaga rasio thumbnail 9:16, dan aturan breakpoint berada di sekitar 1100px, 760px, dan 390px.

Skeleton menggunakan `.skeleton-thumb`, `.skeleton-avatar`, dan `.skeleton-line` dengan `@keyframes skeleton-shimmer`. Geraknya dimatikan untuk pengguna yang memilih `prefers-reduced-motion`. Tombol hapus koleksi memakai `.watched-remove`; aturan `.chips[hidden]` diperlukan karena `.chips` menentukan `display: flex`.

### JavaScript

Urutan alur utama di `anime.js`:

1. Data `animeVideos` didefinisikan.
2. Status riwayat dibaca dari `localStorage`.
3. `renderGallery()` menyaring data berdasar kategori, pencarian, dan mode riwayat.
4. `createCard()` membuat thumbnail dan handler kartu.
5. `showVideo()` membuka player; event `play` menyimpan status ditonton.
6. Handler kategori, pencarian, koleksi, tombol kembali, dan menu mobile didaftarkan.

State kategori disimpan di `activeCategory`; mode koleksi di `viewingWatched`. Ingat untuk mengatur ulang kedua tampilan itu bila menambah jalur navigasi baru.

## Catatan untuk chat berikutnya

- Pertahankan gaya vanilla HTML/CSS/JS kecuali diminta mengubah stack.
- Saat menambah entri, periksa `category` persis `Anime` atau `Film`, judul tidak kosong, poster tersedia, dan sumber video valid.
- Pastikan perubahan JavaScript/CSS mempertahankan perilaku riwayat, pencarian semua judul, batas 13 kartu awal, serta layout mobile.
- Jika aset tidak tampak berubah setelah reload, naikkan query versi di `index.html` atau lakukan hard reload.
- `.gitignore` mengabaikan `*.md`, tetapi `README.md` dikecualikan secara eksplisit.
