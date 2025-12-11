# UAS-DAE-c14250081 - Sharen Adella Florencia
Tentu, berikut adalah ringkasan dan visualisasi dari analisis data universitas Anda dalam format Markdown.

## 📊 Ringkasan Analisis Data Universitas

Analisis ini menggunakan **1.300 universitas di US** dengan tujuan mencari insight (visualisasi) dan melakukan klasifikasi status universitas (**Public (1)** atau **Private (2)**) menggunakan algoritma **Decision Tree**.

---

### 🛠️ Tahap Data Preparation

#### 1. Missing Value Handling
* **Strategi:** Kolom numerik diisi dengan **Mean value**.
* **Tujuan:** Mengisi kekosongan data tanpa membuat bias kategori dan menjaga distribusi data.

#### 2. Column Filter & Normalisasi
* **Data Numerik:** Dinormalisasi menjadi rentang **0 hingga 1**.
    * **Metode:** Min-Max Normalization (contoh: biaya kuliah, jumlah aplikasi, Student/Faculty ratio).
    * **Tujuan:** Menghindari fitur dengan nilai besar mendominasi model dan memastikan semua fitur numerik memiliki "kekuatan setara" agar algoritma klasifikasi bekerja optimal.

#### 3. Transformasi Kolom Target
* **Kolom Target (`Status universitas`):** Diubah dari numerik (1/2) menjadi **String/Kategori** menggunakan node *Number to String*.
    * **Encoding:** 1 -> public, 2 -> private
    * **Alasan:** Decision Tree di KNIME lebih baik membaca kategori string, dan **normalisasi pada target klasifikasi akan menyesatkan model** (misalnya, membuat model berpikir "private (1.0) lebih besar dari public (0.0)").

#### 4. Column Appender
* **Penggabungan:** Menggabungkan kembali **fitur numerik yang sudah dinormalisasi** dengan **kolom target kategori (public/private)**.

### 📈 Visualisasi (insight yang didapat dari visualisasi)
#### 1. Histogram : Sebaran Graduation Rate (Tingkat Kelulusan)
[Histogram Graduation Rate](attachment:IMG_7036.jpeg)
- Mayoritas universitas berada pada **graduation rate tinggi (70–90%)**.
- Hampir tidak ada universitas dengan tingkat kelulusan sangat rendah.
- Hal ini menunjukkan bahwa **kualitas kelulusan secara umum baik** dan **standar akademik terjaga**.
##### Insight
➡️ Tingginya tingkat kelulusan dapat menjadi indikator bahwa:
- Dukungan akademik kepada mahasiswa berjalan efektif.
- Kualitas kurikulum stabil dan relevan.
- Proses seleksi mahasiswa cukup ketat, sehingga mahasiswa yang diterima memiliki kesiapan akademik tinggi.

#### 2. Histogram : Sebaran % Faculty with PhD
- Distribusi persentase dosen bergelar PhD cukup menyebar, namun mayoritas berada pada kisaran **50–80%**.
- Terdapat beberapa outlier dengan persentase dosen PhD **mendekati 95–100%**, menunjukkan kualitas akademik sangat tinggi.
##### Insight
➡️ Universitas dengan persentase dosen PhD lebih tinggi cenderung memiliki:
- Kapasitas riset lebih kuat dan berpengaruh.
- Akses lebih mudah terhadap pendanaan akademik dan hibah penelitian.
- Kualitas pengajaran yang lebih baik dan berbasis riset mutakhir.

#### 3. Scatter Plot: % Faculty PhD vs Graduation Rate
Visualisasi menunjukkan adanya **korelasi positif** antara:
- persentase dosen bergelar PhD, dan  
- tingkat kelulusan mahasiswa (graduation rate).
Semakin tinggi persentase dosen PhD dalam suatu universitas, semakin tinggi pula graduation rate yang dicapai. Meskipun hubungan ini **tidak bersifat linear sempurna**, tren peningkatan terlihat cukup jelas.
##### Interpretasi
➡️ Tingkat pendidikan dosen **berkorelasi langsung** dengan keberhasilan kelulusan mahasiswa.
##### Makna :
- **Kompetensi dosen** mendorong mahasiswa menyelesaikan studi tepat waktu.
- Lingkungan akademik yang lebih berkualitas menciptakan lulusan yang lebih siap secara intelektual dan profesional.
- Dosen bergelar PhD biasanya memiliki kapasitas riset, pedagogi, dan mentoring lebih kuat sehingga mendukung keberhasilan akademik mahasiswa.

#### 4. Bar Chart: Perbandingan Private vs Public
Chart ini membandingkan dua kategori universitas:
- **(Private)** → ditampilkan dengan bar **hijau**
- **(Public)** → ditampilkan dengan bar **biru**
Untuk dua variabel utama:
- **# appl. accepted** (jumlah aplikasi yang diterima)
- **estim. personal $** (perkiraan biaya pribadi mahasiswa)

##### appl. accepted (Jumlah Aplikasi Diterima)
- **Private** memiliki sebaran nilai yang lebih luas dan beberapa puncak yang sangat tinggi.
- Bar hijau tampak lebih bervariasi, menunjukkan bahwa **private menerima lebih banyak aplikasi dalam beberapa kasus**.
- **Public** lebih rendah dan cenderung konsisten, dengan bar biru yang relatif pendek dan stabil.
➡ Kesimpulan
Universitas **Private** memiliki kapasitas penerimaan aplikasi yang lebih besar atau lebih fluktuatif dibandingkan **Public**, yang menunjukkan permintaan yang lebih kuat atau selektivitas lebih fleksibel.

##### estim. personal $ (Perkiraan Biaya Pribadi)
- **Private** secara umum memiliki bar yang jauh lebih tinggi.
- Hal ini memperlihatkan bahwa biaya personal pada kategori **Private** relatif lebih mahal.
- **Public** memiliki bar lebih pendek dan stabil, menunjukkan biaya personal yang lebih rendah.
➡ Kesimpulan 
Biaya personal mahasiswa di **Private** lebih tinggi dibandingkan **Public**, sehingga menunjukkan:
- standar hidup dan fasilitas mungkin lebih premium,
- atau kebijakan pembiayaan kampus yang lebih tinggi.
  
#### 📝 Ringkasan

| Variabel | Private (1) | Public (2) |
|----------|--------------|-------------|
| # appl. accepted | Tinggi & bervariasi | Rendah & stabil |
| estim. personal $ | Cenderung mahal | Relatif lebih terjangkau |
---


### 🧠 Tahap Data Processing & Klasifikasi

#### 1. Table Partitioner
* **Pembagian Dataset:** Dataset dibagi menjadi dua *subset*.
    * **Training Set:** **70%** dari total data.
    * **Testing Set:** **30%** dari total data.
* **Metode Sampling:** **Random sampling** untuk memastikan distribusi data yang acak dan sehingga dapat mewakili keseluruhan populasi.
* **Replikasi:** Opsi **Fixed Random Seed** diaktifkan untuk memastikan pembagian data bersifat **deterministik** dan dapat direplikasi setiap kali *workflow* dijalankan.

#### 2. Decision Tree Classification
* **Algoritma:** **Decision Tree** digunakan untuk memprediksi status universitas.
* **Insight Kecenderungan:**
* **Prediktor Kuat:** Biaya kuliah dan tingkat kelulusan (*Graduation rate*) adalah prediktor terkuat.

#### 3. Scorer (Confusion Matrix Summary)
Tabel ini menunjukkan hasil evaluasi model pada Testing Set.
True Positives (TP) : Jumlah data yang benar diprediksi sebagai kelas tersebut. 
| Kelas Prediksi (RowID) | True Positives (TP) |
| :-------------------- | :------------------ |
| **2 (Private)** | 247 data benar      |
| **1 (Public)** | 132 data benar      |

---

### 📈 Visualisasi Box Plot (True Positives)

Box Plot digunakan untuk memvisualisasikan distribusi statistik dari nilai **True Positives (TP)** untuk kedua kelas.



| Statistik | Kelas 1 (Public) | Kelas 2 (Private) |
| :-------- | :--------------- | :---------------- |
| **Max** | 132              | 247               |
| **Q3** | 132              | 247               |
| **Median**| 69               | 126.5             |
| **Q1** | 6                | 6                 |
| **Min** | 6                | 6                 |

**Catatan Interpretasi:**
* Dalam konteks ini, Box Plot menampilkan distribusi nilai jika kita *menganggap* setiap *True Positive* sebagai sebuah *observasi*.
* Data TP yang Anda berikan adalah **nilai tunggal** untuk setiap kelas (Public=132, Private=247).
* Box Plot yang dihasilkan di atas menyiratkan bahwa, dalam skenario visualisasi data statistik yang Anda inginkan (meskipun hanya berdasarkan dua titik data TP), kelas **Private (2)** memiliki nilai TP yang **lebih tinggi** secara keseluruhan (median dan maksimum) dibandingkan dengan kelas **Public (1)**. Hal ini sejalan dengan hasil Scorer di mana TP untuk Private (247) jauh lebih banyak daripada TP untuk Public (132).
