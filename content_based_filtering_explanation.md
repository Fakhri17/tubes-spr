# Penjelasan Content-Based Filtering untuk Sistem Rekomendasi Anime

## 📚 Daftar Isi
1. [Pengenalan Content-Based Filtering](#pengenalan-content-based-filtering)
2. [Konsep Dasar](#konsep-dasar)
3. [Alur Kerja Implementasi](#alur-kerja-implementasi)
4. [Penjelasan Kode](#penjelasan-kode)
5. [Cara Kerja Sistem](#cara-kerja-sistem)
6. [Kelebihan dan Kekurangan](#kelebihan-dan-kekurangan)

---

## Pengenalan Content-Based Filtering

**Content-Based Filtering** adalah teknik rekomendasi yang merekomendasikan item berdasarkan kemiripan konten atau fitur dari item tersebut. Sistem ini menganalisis karakteristik item (seperti genre, tipe, rating) untuk menemukan item serupa tanpa memerlukan data rating dari user lain.

### Perbedaan dengan Collaborative Filtering

| Aspek | Content-Based Filtering | Collaborative Filtering |
|-------|------------------------|------------------------|
| **Basis Rekomendasi** | Fitur item (genre, tipe, dll) | Pola rating user |
| **Data yang Dibutuhkan** | Informasi item saja | Rating/histori user |
| **Cold Start Problem** | Tidak ada masalah untuk item baru | Sulit untuk user/item baru |
| **Diversitas** | Cenderung homogen (genre sama) | Lebih beragam |

---

## Konsep Dasar

Dalam implementasi ini, sistem menggunakan:

1. **TF-IDF (Term Frequency-Inverse Document Frequency)**: Teknik untuk mengubah teks genre menjadi vektor numerik
2. **Cosine Similarity**: Metrik untuk mengukur kemiripan antara dua anime berdasarkan vektor genre mereka
3. **Genre sebagai Fitur Utama**: Genre anime digunakan sebagai dasar untuk menemukan anime serupa

---

## Alur Kerja Implementasi

```
1. Load Data (anime.csv)
   ↓
2. Preprocessing Data
   - Menghapus missing values
   - Membersihkan nama anime
   ↓
3. Ekstraksi Fitur dengan TF-IDF
   - Mengubah genre menjadi vektor numerik
   ↓
4. Menghitung Similarity Matrix
   - Cosine similarity antar semua anime
   ↓
5. Fungsi Rekomendasi
   - Mencari anime terdekat berdasarkan similarity
   ↓
6. Hasil Rekomendasi
```

---

## Penjelasan Kode

### 1. Import Library yang Diperlukan

```python
from sklearn.metrics.pairwise import cosine_similarity
from sklearn.feature_extraction.text import TfidfVectorizer
```

**Penjelasan:**
- `TfidfVectorizer`: Mengubah teks genre menjadi vektor numerik menggunakan teknik TF-IDF
- `cosine_similarity`: Menghitung kemiripan kosinus antara vektor-vektor

### 2. Preprocessing Data

```python
# Menghapus missing values
anime = anime.dropna()

# Membersihkan nama anime
def text_cleaning(text):
    if not isinstance(text, str):
        return text
    text = html.unescape(text)
    text = re.sub(r'["\']', '', text)
    text = re.sub(r'\.hack//', '', text)
    text = re.sub(r'&', 'and', text)
    return text

anime['name'] = anime['name'].apply(text_cleaning)
```

**Penjelasan:**
- Menghapus data yang tidak lengkap untuk memastikan kualitas data
- Membersihkan nama anime dari karakter khusus dan HTML entities

### 3. Ekstraksi Fitur dengan TF-IDF

```python
# Membuat objek TF-IDF Vectorizer
tf = TfidfVectorizer()

# Fit dan transform genre menjadi matriks TF-IDF
tfidf_matrix = tf.fit_transform(anime['genre'])
```

**Penjelasan:**
- `TfidfVectorizer()`: Membuat objek untuk transformasi TF-IDF
- `fit_transform()`: 
  - **Fit**: Mempelajari vocabulary dari semua genre yang ada
  - **Transform**: Mengubah setiap genre menjadi vektor numerik
  
**Contoh:**
Jika genre anime adalah `"Action, Adventure, Fantasy"`, maka akan diubah menjadi vektor numerik di mana:
- Setiap genre menjadi dimensi dalam vektor
- Nilai TF-IDF menunjukkan seberapa penting genre tersebut untuk anime tersebut

**Output:**
- `tfidf_matrix`: Matriks sparse dengan shape `(jumlah_anime, jumlah_genre_unik)`
- Setiap baris mewakili satu anime dalam bentuk vektor numerik

### 4. Menghitung Cosine Similarity

```python
# Menghitung cosine similarity pada matrix tf-idf
cosine_sim = cosine_similarity(tfidf_matrix)

# Membuat DataFrame untuk memudahkan akses
cosine_sim_df = pd.DataFrame(
    cosine_sim, 
    index=anime['name'], 
    columns=anime['name']
)
```

**Penjelasan:**
- `cosine_similarity(tfidf_matrix)`: Menghitung kemiripan kosinus antara semua pasangan anime
- Cosine similarity mengukur sudut antara dua vektor:
  - Nilai 1 = sangat mirip (sudut 0°)
  - Nilai 0 = tidak mirip (sudut 90°)
  - Nilai -1 = sangat berbeda (sudut 180°)

**Rumus Cosine Similarity:**
```
cos(θ) = (A · B) / (||A|| × ||B||)
```

**Output:**
- `cosine_sim_df`: Matriks similarity dengan shape `(12017, 12017)`
- Setiap sel `[i, j]` berisi nilai similarity antara anime i dan anime j

### 5. Fungsi Rekomendasi

```python
def anime_recommendations(name, similarity_data=cosine_sim_df, 
                         items=anime[['name', 'genre']], k=10):
    
    # Mengambil index anime yang paling mirip
    index = similarity_data.loc[:,name].to_numpy().argpartition(
        range(-1, -k, -1))
    
    # Mengambil k+1 anime teratas (termasuk anime itu sendiri)
    closest = similarity_data.columns[index[-1:-(k+2):-1]]
    
    # Drop nama anime yang dicari agar tidak muncul dalam rekomendasi
    closest = closest.drop(name, errors='ignore')
    
    # Merge dengan data genre untuk menampilkan informasi lengkap
    return pd.DataFrame(closest).merge(items).head(k)
```

**Penjelasan Langkah demi Langkah:**

1. **`similarity_data.loc[:,name]`**: 
   - Mengambil kolom similarity untuk anime yang dicari
   - Menghasilkan Series dengan similarity score ke semua anime lain

2. **`.to_numpy().argpartition(range(-1, -k, -1))`**:
   - `argpartition`: Mengurutkan index berdasarkan nilai similarity
   - `range(-1, -k, -1)`: Mengambil k+1 nilai terbesar (karena -1 sampai -(k+1))
   - Lebih efisien daripada `argsort()` karena tidak perlu mengurutkan semua nilai

3. **`closest = similarity_data.columns[index[-1:-(k+2):-1]]`**:
   - Mengambil nama anime dengan similarity tertinggi
   - `[-1:-(k+2):-1]`: Mengambil dari yang terbesar sampai k+1 terbesar (reverse order)

4. **`.drop(name, errors='ignore')`**:
   - Menghapus anime yang dicari dari daftar rekomendasi
   - `errors='ignore'`: Tidak error jika nama tidak ditemukan

5. **`.merge(items).head(k)`**:
   - Menggabungkan dengan data genre untuk informasi lengkap
   - Mengambil k rekomendasi teratas

### 6. Contoh Penggunaan

```python
# Mencari rekomendasi untuk "Dragon Ball Z"
anime_recommendations('Dragon Ball Z')
```

**Output:**
Sistem akan mengembalikan 10 anime yang memiliki genre paling mirip dengan "Dragon Ball Z", diurutkan berdasarkan similarity score tertinggi.

---

## Cara Kerja Sistem

### Contoh Praktis

Misalkan kita mencari rekomendasi untuk **"Dragon Ball Z"**:

1. **Input**: Nama anime "Dragon Ball Z"

2. **Proses**:
   - Sistem mencari vektor TF-IDF untuk "Dragon Ball Z"
   - Membandingkan vektor tersebut dengan semua anime lain menggunakan cosine similarity
   - Mengurutkan anime berdasarkan similarity score

3. **Output**: 
   - 10 anime dengan similarity tertinggi
   - Biasanya anime dengan genre yang sama atau mirip (Action, Adventure, Shounen)

### Visualisasi Proses

```
Anime Input: "Dragon Ball Z"
Genre: "Action, Adventure, Comedy, Fantasy, Shounen, Super Power"
         ↓
    [TF-IDF Vector]
    [0.2, 0.5, 0.1, 0.8, 0.6, 0.3, ...]
         ↓
    Cosine Similarity dengan semua anime
         ↓
    Ranking berdasarkan similarity score
         ↓
    Top 10 Rekomendasi:
    1. "Dragon Ball" (similarity: 0.95)
    2. "Naruto" (similarity: 0.87)
    3. "One Piece" (similarity: 0.82)
    ...
```

---

## Kelebihan dan Kekurangan

### ✅ Kelebihan Content-Based Filtering

1. **Tidak Memerlukan Data User**
   - Dapat bekerja hanya dengan informasi item
   - Tidak perlu data rating dari banyak user

2. **Mengatasi Cold Start untuk Item Baru**
   - Item baru dapat langsung direkomendasikan jika memiliki fitur yang diketahui
   - Tidak perlu menunggu banyak rating

3. **Transparan dan Dapat Dijelaskan**
   - User dapat memahami mengapa item direkomendasikan
   - "Kami merekomendasikan ini karena genre-nya mirip dengan yang Anda suka"

4. **Tidak Terpengaruh Popularitas**
   - Dapat merekomendasikan item niche yang sesuai dengan preferensi

### ❌ Kekurangan Content-Based Filtering

1. **Keterbatasan Diversitas**
   - Cenderung merekomendasikan item dengan genre yang sama
   - Sulit menemukan item di luar "comfort zone" user

2. **Ketergantungan pada Kualitas Fitur**
   - Jika fitur (genre) tidak lengkap atau tidak akurat, rekomendasi akan buruk
   - Perlu ekstraksi fitur yang baik

3. **Over-Specialization**
   - User mungkin hanya melihat item dengan karakteristik yang sama
   - Kurang eksplorasi ke genre baru

4. **Tidak Memanfaatkan Data User**
   - Mengabaikan informasi berharga dari preferensi user lain
   - Tidak dapat belajar dari pola rating user

---

## Kesimpulan

Sistem rekomendasi anime ini menggunakan **Content-Based Filtering** dengan:

- **Fitur utama**: Genre anime
- **Teknik**: TF-IDF Vectorization + Cosine Similarity
- **Output**: Rekomendasi anime berdasarkan kemiripan genre

Sistem ini efektif untuk memberikan rekomendasi berdasarkan karakteristik item itu sendiri, tanpa memerlukan data rating dari user lain. Namun, untuk meningkatkan kualitas rekomendasi, dapat dikombinasikan dengan **Collaborative Filtering** atau menggunakan teknik **Hybrid Filtering**.

---

## Referensi

- Scikit-learn Documentation: [TfidfVectorizer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)
- Scikit-learn Documentation: [cosine_similarity](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.cosine_similarity.html)
- Dataset: [Kaggle: Anime Recomendations Database](https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database)

