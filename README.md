# UAS-DAE-c14250081 - Sharen Adella Florencia
Tentu, berikut adalah ringkasan dan visualisasi dari analisis data universitas Anda dalam format Markdown.

## 📊 Ringkasan Analisis Data Universitas

Analisis ini menggunakan **1.300 universitas di US** dengan tujuan melakukan klasifikasi status universitas (**Public (1)** atau **Private (2)**) menggunakan algoritma **Decision Tree**.

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
    * **Private (2) Cenderung:** Biaya kuliah **lebih tinggi**, *Acceptance rate* **lebih rendah**, *Size* mahasiswa **lebih kecil**, *Graduation rate* **lebih tinggi**.
    * **Public (1) Cenderung:** Biaya **lebih rendah**, Populasi mahasiswa **lebih besar**, *Student/Faculty ratio* **lebih besar**.
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
