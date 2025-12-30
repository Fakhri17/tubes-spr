# Sistem Rekomendasi Anime Menggunakan Content-Based Filtering

![Anime Recommendation System](https://images.unsplash.com/photo-1578632767115-351597cf2477?w=800&h=400&fit=crop)

---

## 📖 Latar Belakang

Anime telah menjadi salah satu bentuk hiburan yang sangat populer di seluruh dunia, dengan ribuan judul yang tersedia di berbagai platform streaming. Namun, dengan banyaknya pilihan yang ada, penonton sering kali kesulitan menemukan anime yang sesuai dengan preferensi dan minat mereka. 

Masalah ini menjadi lebih kompleks mengingat:
- **Volume data yang besar**: Dataset yang digunakan mencakup **12,294 anime** dari **73,516 pengguna**
- **Beragam genre**: Terdapat ribuan kombinasi genre yang berbeda
- **Kebutuhan personalisasi**: Setiap pengguna memiliki preferensi yang unik

Oleh karena itu, diperlukan sistem rekomendasi yang dapat membantu pengguna menemukan anime yang relevan berdasarkan karakteristik konten anime itu sendiri, tanpa harus bergantung pada data rating dari pengguna lain.

---

## 🎯 Tujuan Proyek

Proyek ini bertujuan untuk:

1. **Mengembangkan sistem rekomendasi anime** menggunakan metode Content-Based Filtering
2. **Memanfaatkan fitur konten anime** (genre) untuk menemukan anime serupa
3. **Memberikan rekomendasi yang relevan** berdasarkan kemiripan karakteristik anime
4. **Mengatasi cold start problem** untuk anime baru tanpa perlu menunggu banyak rating

---

## 🔬 Metodologi

Sistem rekomendasi ini menggunakan pendekatan **Content-Based Filtering** dengan langkah-langkah sebagai berikut:

### 1. Preprocessing Data
- Membersihkan data dari missing values
- Menghapus duplikat
- Membersihkan nama anime dari karakter khusus dan HTML entities

### 2. Ekstraksi Fitur dengan TF-IDF
- Menggunakan `TfidfVectorizer` untuk mengubah genre anime menjadi vektor numerik
- Setiap genre menjadi dimensi dalam ruang vektor
- Nilai TF-IDF menunjukkan pentingnya genre untuk setiap anime

### 3. Perhitungan Similarity
- Menghitung **Cosine Similarity** antara semua pasangan anime
- Cosine similarity mengukur kemiripan berdasarkan sudut antara vektor genre
- Menghasilkan matriks similarity berukuran (12,017 × 12,017)

### 4. Fungsi Rekomendasi
- Mencari k anime terdekat berdasarkan similarity score
- Mengurutkan hasil berdasarkan tingkat kemiripan tertinggi
- Menampilkan informasi lengkap (nama dan genre) dari anime yang direkomendasikan

![Anime Characters](https://images.unsplash.com/photo-1607604276583-eef5d076aa5f?w=800&h=300&fit=crop)

---

## 📊 Dataset

Proyek ini menggunakan dataset dari **MyAnimeList.net** yang terdiri dari:

### Anime.csv
- **12,294 anime** dengan informasi lengkap
- Kolom: `anime_id`, `name`, `genre`, `type`, `episodes`, `rating`, `members`

### Rating.csv
- **7,813,736 rating** dari 73,516 pengguna
- Kolom: `user_id`, `anime_id`, `rating`

### Statistik Dataset
- Jumlah judul anime unik: **12,294**
- Jumlah genre unik: **3,265**
- Jumlah pengguna: **73,516**
- Setelah preprocessing: **12,017 anime** (setelah menghapus missing values)

---

## 💻 Implementasi

### Teknologi yang Digunakan
- **Python** sebagai bahasa pemrograman utama
- **Pandas** untuk manipulasi data
- **Scikit-learn** untuk TF-IDF dan Cosine Similarity
- **NumPy** untuk operasi numerik
- **Matplotlib** untuk visualisasi data

### Alur Kerja Sistem

```
Input: Nama Anime
    ↓
Ekstraksi Genre → TF-IDF Vectorization
    ↓
Perhitungan Cosine Similarity
    ↓
Ranking Anime Berdasarkan Similarity
    ↓
Output: Top 10 Rekomendasi Anime
```

---

## 📈 Hasil

Sistem berhasil memberikan rekomendasi anime yang relevan berdasarkan kemiripan genre. Contoh hasil rekomendasi:

### Contoh 1: Rekomendasi untuk "Dragon Ball Z"
Sistem akan merekomendasikan anime dengan genre serupa seperti:
- Action, Adventure, Shounen
- Anime dengan elemen Super Power dan Fantasy

### Contoh 2: Rekomendasi untuk "Shingeki no Kyojin"
Sistem akan merekomendasikan anime dengan karakteristik:
- Action, Drama, Fantasy
- Anime dengan tema serius dan intens

### Kelebihan Sistem
✅ **Tidak memerlukan data user** - Dapat bekerja hanya dengan informasi anime  
✅ **Mengatasi cold start** - Anime baru dapat langsung direkomendasikan  
✅ **Transparan** - User dapat memahami alasan rekomendasi  
✅ **Cepat dan efisien** - Perhitungan similarity dilakukan sekali untuk semua anime

---

## 🎓 Kesimpulan

Proyek ini berhasil mengimplementasikan sistem rekomendasi anime menggunakan **Content-Based Filtering** dengan memanfaatkan fitur genre sebagai dasar rekomendasi. 

### Poin Penting:

1. **Content-Based Filtering efektif** untuk sistem rekomendasi anime karena:
   - Genre merupakan indikator yang kuat untuk kemiripan anime
   - Tidak memerlukan data rating dari banyak pengguna
   - Dapat memberikan rekomendasi yang personal berdasarkan karakteristik item

2. **TF-IDF + Cosine Similarity** merupakan kombinasi yang tepat untuk:
   - Mengubah teks genre menjadi representasi numerik
   - Mengukur kemiripan antar anime secara akurat
   - Menghasilkan rekomendasi yang relevan

3. **Keterbatasan sistem** yang perlu diperhatikan:
   - Cenderung merekomendasikan anime dengan genre yang sama (kurang diversitas)
   - Bergantung pada kualitas dan kelengkapan data genre
   - Tidak memanfaatkan informasi dari preferensi pengguna lain

### Saran Pengembangan Selanjutnya:

- **Hybrid Filtering**: Menggabungkan Content-Based dengan Collaborative Filtering untuk hasil yang lebih baik
- **Multi-fitur**: Menambahkan fitur lain seperti sinopsis, tahun rilis, atau studio
- **Deep Learning**: Menggunakan teknik embedding untuk representasi yang lebih baik
- **Evaluasi Metrik**: Menambahkan metrik evaluasi seperti precision, recall, atau RMSE

---

<!-- ## 📚 Referensi

- Dataset: [Kaggle - Anime Recommendations Database](https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database)
- MyAnimeList.net API
- Scikit-learn Documentation: TfidfVectorizer & cosine_similarity

---

*Dibuat untuk memenuhi kebutuhan sistem rekomendasi anime yang efektif dan mudah digunakan.* -->

